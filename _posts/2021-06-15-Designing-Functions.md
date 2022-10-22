---
title: Designing Functions
author: Dave Amiana
date: 2021-06-15
categories: [Programming, Computer Science, C++]
tags: [programming, Computer Science, C++]
math: true
mermaid: true
---

> back to basics

A function is an essential construct in programming. In the language that we use to convey a message on the computer, functions closely resemble a verb: _"the nuts and bolts of the language"_ (S. Pinker, 2007). Put it in another way, functions serve as the primary constructs for expressing a set computational procedure in a programming language.

---

It is better to break down the problem into manageable pieces. Solving the smaller part of the problem requires a computational procedure that we store in a function.

This article will introduce the things to consider in designing and organizing functions.

A function is a block of code that processes inputs and configures an output. One of the nice things about this is it breaks down our solution into a set of subroutines that we can reuse to solve other problems.

Indeed, simple algorithms can be stored in one function, sometimes, it branches off into two or more: **the question is when and why do we need indirection?** The most obvious reason for this is _code-reusability_. We need to make conscious efforts into making our codebase sustainable i.e. reusable. To achieve this, we have to delegate the simplest version of a task in a function. Our goal is to minimize the _boilerplate code_ (sections of code that are repeated in multiple places with little to no variation), **we follow the virtue of not repeating ourselves**.

Functions are sufficient for developing sophisticated software systems. This technique is sometimes known as _modular programming_. This is one of the reasons why C is deemed elegant by some developers I know.

There are three major concepts in modular programming:

- _Module_ - a self-contained collection of functions that solves a problem (or subproblem).
- _Abstract Step_ - A step in which some implementation details are unspecified. This step may define the routes where concrete functions may take place. In other words, it is a code block that delegates a group of functions necessary for solving the problem.
- _Concrete Step_ - A realization of the abstract step that implements the logic for solving independent subproblems.

---

## Software Design Process: Top-Down Approach

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1623724225735/nkFCw94Pc.png)
`Figure 1.0`. Hierarchical solution tree (Dale, N. B., & Weems, C., 2014).

Steps for applying design principles in functions:

1.  **Understand the problem**. State its input requirements as well as the specifications for expected output.
2.  **Write the major steps for solving the problem**. Make a hierarchical solution tree (as seen in figure 1) and define the major problems you need to solve. As you go down to the bottom, problems become more concrete and simpler.
3.  **Decide if the problem can be broken down into independent subproblems that we can work on separately**. It is often simple enough when you can see how to implement it directly in a language. If you feel overwhelmed by writing it in a language, you are probably bypassing one or more levels of abstraction -- you need to step back and think through how you can simplify this by breaking down the problem.
4.  **Ensure that each function has exactly one job**. Their purpose should be obvious in its name (it makes your code easier to read and self-documenting, so we must assure that the function name should clearly state its purpose).
5.  **Document solution process**. Documentation helps to make our codebase easier to maintain.

---

## References:

1. Pinker, S. (2007). Steven Pinker: What our language habits reveal. [TED](https://www.youtube.com/watch?v=LjQM8PzCEY0).
2. Dale, N. B., & Weems, C. (2014). Programming and problem solving with C++. Jones & Bartlett Publishers.
3. DeNero, J. (n.d.). [Composing Programs](https://composingprograms.com/pages/14-designing-functions.html).
