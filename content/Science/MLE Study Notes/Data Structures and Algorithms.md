Python stacks and queues use collections.deque -> linked-list based
- Same for doubly linked list
Operations to know
- len(D)
- D.appendleft(e)
- D.append(e)
- D.popleft(e)
- D.pop()


Hash table in python is implemented as dict
- Remember, separate chaining (bucket has its own container) and open addressing to deal with collisions
- Python uses random probing vs linear/quadratic probing


Trees
- Binary tree - <= 2 children
- Binary search tree - all left descendents <= n <= right descendents
- Complete binary tree - every level fully filled except rightmost descendents on last level
- Full binary tree - Each node has either 0 or 2 children
- Perfect binary tree - every level filled
Tree vs Graph?
- Tree is just a restricted DAG - child can only have one parent
- Valid tree has n-1 edges from nodes
Traversing a tree:
- DFS - preorder, inorder, postorder
![[Pasted image 20260329234812.png]]
- BFS
![[Pasted image 20260329234834.png|393]]
Traversing a graph vs Tree
- For graph, need to mark nodes visited in DFS otherwise might be infinite loops
![[Pasted image 20260329235012.png]]
3 ways to describe a graph
- Directly in node class
 ![[Pasted image 20260330000209.png|197]]
- Adjacency list
- Adjacency matrix


Heaps imported from heapq
- Python defaults to min heap - make value negative for max heap (negate heappush and then negate after heappop)
- heapq.heappush(heap, i) - Insert is O(log n) - Add element to bottom and bubble up swapping
- heapq.heappop(heap) - Extract min, replace with bottom right element, bubble down
- Keep track of top-k largest element - do this using min heap, when heap is full (at k numbers), add element and then remove smallest


Priority Queue - takes {key, value} pairs, then removing elements starting with minimum key
- Implement with unsorted linked list, sorted linked list and min heap

Trie/Prefix tree
- n-ary tree - commonly for storing english language, quick prefix lookups

Python Set
- Unordered, have unique elements, and mutable
- Use X = set() or X = {1,2,3} or X = set(array)


Dynamic Programming
- Top-down approach (memoization) - recursive approach, solve by solving subproblems and storing subproblem results (memoized) so do not need to recompute
- Bottom-up approach (Tabulation) - solve all related subproblems first, then build up solution to larger problems
- Best way to show difference - Fibonacci
	- Memoization (recursion) - return fib(n-1) + fib(n-2)
	- Tabulation (start with n=1, n=2, start solving n=3)


Binary Search Implementation
![[Pasted image 20260330000704.png|379]]


Topological Sort
- Take a directed graph - return array of nodes where each node appears before all the nodes it points to
- Useful for solving scheduling - compilation in makefiles for example