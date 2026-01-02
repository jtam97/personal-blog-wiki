---
title: Laplace Transform
---

> [!tip] Information from 3Blue1Brown video series on Laplace Transform

## Notes:

### Basics of Laplace Transform
**Starting off with exponential functions**
- Note that the exponential function e^t is hugely important in explaining Euler's formula. First the derivative of e^t is e^t

Intuition using position and velocity vectors over time
- d/dt e^t = e^t means velocity is always equal to position
- d/dt e^2t = 2e^2t means velocity is always twice the position
- d/dt e^-0.5t = -0.5t e^-0.5t. The velocity vector is backwards and half of the position vector

- Here is the interesting part - d/dt e^it (multiplying by an imaginary number) = i e^it 
	- Geometrically, multiplying i to a vector is like making a 90 degree rotation
	- This means e^it traces a circle as the velocity vector is always perpendicular to the position vector (famously, e^i pi = -1)
- So if e^it is a circle, what does e^2i t do? It still draws a circle but rotated twice as fast!
	- That scalar is commonly labelled as omega (w) for angular momentum, in e^i w t

- Lets look at an example with both a real and imaginary number component e^(-0.5 +i)t.
	- Exponential functions split into (e^-0.5t)(e^it) - this means there is an exponential decay component and a circular component that goes around once circle in 2pi.

Now intuition using a harmonic oscillator (like a spring with a mass on it)
- The equation of a spring is ma = -kx - uv where ma is Force, -kx is the spring force and -uv is the damping force
- The equation can basically be mx''(t) + ux'(t) + kx(t) = 0 and a solution for this if x(t) = e^st,
	s = +/- i * sqrt(k/m)
- Yes, since theres both a positive and negative solution, if we combine both of the vectors, it actually comes out to an oscillating solution on the real number line.
- Notice that the oscillation behavior of the function could be represented by sin(t) and cos(t) and the frequency + amplitude could be altered too
	- However, not generalizable when you add the damping coefficient
	- Even further, what if we generalize to some polynomial function a_n x^n(t) + ... + a_2 x''(t) + a_1 x'(t) + a_0 x(t) = 0?

Polynomial functions
- The polynomial broken down into solutions reveals that there are solutions for each s_0,s_1, s_2,... and you can freely change the coefficients to alter how the function oscillates and moves
- However, this doesn't work in solving real world equations because the solution itself is constrained and depends on the coefficient
- The key thing to note though is that many real world equations DO have some function that solves it from a polynomial equation, just that the coefficients are specific and difficult to solve for
	- You also get the intuition that there is a solution for these equations as long as you allow s to be complex

The question to ask then is for a function x(t) and a differential function describing it, how do we know how to break down the parts of the exponential function?
- Forced harmonic oscillator - how do we know the solution is four exponentials, how do we solve for s and how do we solve for coefficients?

