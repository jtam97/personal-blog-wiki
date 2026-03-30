
==**Architecture**==
- Attention mechanism computes how much to attend to every other token. 
- Attention is O(n^2) in sequence length, scaling bottleneck motivates flash attention and sparse attention

**Positional encodings**
Attention is permutation-invariant (does not know token order) means position must be injected
Different methods for encoding:
- Sinusoidal - fixed sin and cos at each position; no learned params and poor extraction
- RoPE (rotary positional embedding) - encodes positions by rotating Q and K vectors in 2D subspaces - relative positions fall out naturally from dot product 
	- excellent length extrapolation. Used in LLaMA and GPT-NeoX
- ALiBi (Attention with linear biases) - Adds negative linear bias to attention logits based on distance between tokens - penalizes attending far away. 
	- Extremely good at length generalization beyond training

**KV caching**
During autoregressive generation, at each new token you would normally need to recompute keys and values for ALL previous tokens which is super wasteful.
- Instead cache K and V matrices from previous forward passes and only need to compute K and V for new token and run attention against cache
- Simply just storing previous calculations and reusing them later to recompute attention

**Pretraining objectives**
- MLM - masked learning modeling - BERT paradigm randomly mask a percent of tokens and train the model to predict them using BIDIRECTIONAL context
	- BERT paradigm - rich contextual representations but not natively generative, can't autoregressively sample from a BERT model
	- BERT can see all tokens at once - every token attends to every other token in both directions, making it unable to be generative because it was TRAINED to use all tokens on either side
	- **ESM-2 is a BERT style model** - why it creates a rich embedding for a known sequence
- CLM - Causal language modeling - GPT paradigm predicts the next token given ALL of the previous tokens
	- Uses causal mask to prevent attention from seeing the future, making it natively generative
	- All GPTs use this - best for generation, in-context learning and instruction following
	- **NOTE**: in-context learning (ICL) is when a model learns to perform a new task purely from examples provided in the prompt - no gradient updates, no fine-tuning - Also called **few-shot prompting**(a few examples) or **zero-shot** (just instructions, no examples)
- Span corruption - T5/encoder-decoder paradigm - mask a contiguous *span* of tokens (not individual tokens), replace each span with a single sentinel token, and train decoder to reconstruct original spans.
	- Basically, a more aggressive training method than MLM, encouraging model to generate multi-token completions.
	- Used in T5 and basis for bio models like ESM-3
	- The concept of this is SIMILAR to diffusion, with one slight difference - span corruption does one step of corruption and you restore it VS diffusion where you do many steps of corruption
		- Similar idea to having a painting with three spots of black paint and you restore the painting VS practicing restoring the painting for every step of damage


**NOTE for clarification** - 
- BERT is encoder only, no decoder. It reads sequence bidirectionally and produces context embeddings, no generation. 
- T5, BART and the original transformer are encoder-decoder models. Encoder reads bidirectionally; decoder generates output autoregressively, attending to both previous outputs (causal self-attention) and encoder's representation (cross-attention). This model is great for tasks where you have a clear input -> output structure.
	- Examples are translation and summarization
- GPT is decoder-only. No encoder, no cross-attention. Just a stack of causally-masked self-attention layers. The entire context - instructions, examples, and conversation history - is flattened into a sequence and processed left to right.
	- Great for generation, ICL (in-context learning), chatting
Why not use encoder-decoder models everywhere?
- First, because decoder-only models are surprisingly good at "understanding" too.
- Much more practical in pretraining objective - In encoder-decoder models, only decoder positions generate signal, and you need structured input-output pairs. Decoder-only models can train on raw unstructured text at internet scale with no labeling.

**Scaling laws**
- Optimal training follows the rule: tokens approximately 20x parameters - known as the Chinchilla Laws (based on 2022 DeepMind Chinchilla papers)
- So doubling model size should follow doubling training data - although in reality, inference cost matters so having more tokens than "optimal" is better

**Mixed precision**
- Store weights in half precision to halve memory and increase throughput - you keep a master copy of weights in FP32 for numerically stable gradient updates, then cast back to original
**Gradient Checkpointing**
- Also called activation recomputation - during forward pass, intermediate activations NORMALLY stored for backprop - this is SUPER memory-expensive
- Instead, discard activation during forward pass and RECOMPUTE them during backprop
- Tradeoff: 30% more compute, dramatically reduced memory
- Functions like dividing the activation stores into smaller pieces, and each time you backprop through a "section", you recompute activation
**ZeRO Optimization**
- Normally, standard data parallelism means every GPU holds a full copy of optimizer states, gradients and parameters
- Instead ZeRO shards these across GPUS
	- ZeRO-1 = Shards optimizer states
	- ZeRO-2 = ZeRO-1 + gradients
	- ZeRO-3 = ZeRO-2 + parameters


**Large-Scale Training Infrastructure**

**Model parallelism**
- Tensor parallelism - split individual weight matrices across GPUs
- Pipeline parallelism - split model by layers
- Sequence parallelism - split sequence dimension across GPUs
**Data parallelism**
- FSDP (Fully sharded data parallel) - PyTorch version of ZeRO-3
- DeepSpeed ZeRO - Microsoft implementation
**Multi-node Training**
- NCCL (Nvidia Collective Communications Library) - a library to implement GPU-to-GPU collective operations
- InfiniBand - High bandwidth, low latency interconnect for multi-node GPU clusters - high parallelism
**Gradient accumulation & LR Schedules**
- Normal stuff - LR warmup, cosine decay, and linear decay

**Fine-tuning & Adaptation**
- LoRA (Low rank adaptation) - instead of updating full weight matrix W (d x k), freeze W and add a low-rank bypass deltaW = AB where A is d x r and B is r x k. Only A and B are trained - typically 0.1-1% of full model parameters
- QLoRA (Quantized LoRA) - LoRA applied to a quantized model; base model frozen in 4-bit
- Adaptive layers - Insert small trainable bottleneck modules (linear -> nonlinearity -> linear) inside each transformer layer, typically added after attention and FFN.
- Old approach replaced by LoRA because LoRA adds zero inference latency when merged

**RLHF**
Reinforcement learning with human feedback - the pipeline that made ChatGPT work
1. SFT (supervised fine-tuning) - fine-tune base model on high-quality demonstrations
2. Reward model training - Collect human preference pairs (AB testing basically); train a scalar reward model (separate neural network) to predict human preference
	- Outputs a scalar score: "How good is this response?"
3. PPO training (proximal policy optimization) - Use reward model as the reward signal, optimize policy (the LLM) with PPO, with a KL penalty against the SFT model to prevent reward hacking
	- The policy is the LLM - takes a prompt and produces response
	- The action is generating next token
	- The reward is the scalar score from reward model, given at the end of a complete response
The problem - *Reward hacking*
- Reward model is an imperfect proxy for human preference - trained on finite set of human labels and has blind spots
- If you over-optimize, model will find degenerate responses that score highly but are actually bad. For example:
	- Responses extremely verbose because reward model learned length correlates to quality
	- Repetitive confident-sounding text that pattern-matches to "good answers" rather than being correct
	- Subtle sycophantic phrasing (overly or insincere flattery) exploits reward model biases

KL Penalty - The Fix
- To prevent reward hacking, add a KL divergence penalty between current LLM (policy being trained) and frozen SFT model
	Total objective = Reward score - Beta * KL(LLM_current || LLM_SFT)
- KL divergence measures how different current LLM output is from the SFT model distribution
	- If LLM starts generating VERY different responses, KL term grows large and penalizes objective
	- High beta - stay close to SFT, low beta - allow more deviation

**RLAIF**
Reinforcement learning with AI feedback - replace human feedback with a strong AI model, generating preference judgements
- Constitutional AI (Anthropic) is a canonical example - a "critic" model evaluates responses against a set of principles
- Scales better than human labeling; quality depends on feedback model alignment

**Instruction tuning vs Task Specific Tuning**
Instruction tuning - Fine tune on diverse collection of (instruction, response) pairs across *many* tasks
- Goal is generalization to new instructions, not mastering one task
- Produces models that follow natural language instructions flexibly
Task-specific fine-tuning - fine-tune on a single task's labeled dataset (e.g. sentiment classification, protein function prediction)
- Higher performance on specific task, but no generalization

**Catastrophic forgetting**
When you fine-tune a pretrained model on new data, it tends to overwrite previously learned representations, especially if fine-tuning data is narrow or learning rate is high
- Example of catastrophic forgetting - fine-tuning ESM-2 on a narrow protein family, you risk losing general sequence representation
*Mitigation strategies*
- Low learning rate
- LoRA/Adapters
- Replay/mixed training - include samples from original pretraining distribution as well as fine-tuning data
- EWC (Elastic weight consolidation) - Add regularization term penalizing changes to weights that were important for prior tasks
- Progressive training - Gradually introduce new task data with original data


