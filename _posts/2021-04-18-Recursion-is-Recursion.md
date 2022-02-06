---
title: Recursion is Recursion
author:
  name: Dave Amiana
date: 2021-04-18
categories: [Programming, Computer Science]
tags: [programming]
math: true
mermaid: true
---

> Let's talk about one of the cornerstones of Computer Science 

The idea of recursion has a dedicated field in Computer Science called [Recursion theory](https://en.wikipedia.org/wiki/Computability_theory), since we can't possibly cover the entirety of this field, we will just go over some of the key features that most computer science people have grown and love. 

The idea that self-referent entities can reproduce themselves resonates almost entirely in Computer Science. Let's take simple examples and realize the elegance of recursion. Consider the \\(n!\\) function. 

$$n! = n_k \times ... \times n_1 \times n_0\ |\ k\in \mathbb{N}$$

Another way of expressing this function is:

$$\operatorname {fact} (n)={\begin{cases}1&{\mbox{if }}n=0 \\\\ n\cdot \operatorname {fact} (n-1)&{\mbox{if }} n>0 \end{cases}}$$

The computation of a factorial function takes in a number and binds that number with the resolution of a factorial function with a number - 1. Consider the pseudocode snippet below:

```
function factorial(int:a):
    if a == 0 return 1
    return a * factorial(a-1)
```
Let's implement the recursive function using a while-loop -- this approach is called *iterative*.

```
function factorial(int: a):
    int result = 1
    while a > 1: 
        result *= a
        a -= 1
```

Let's break down this code a little bit. Recursive function call mainly has two parts: a *base case*, and an *inductive case*. The base case specifies when the recursion reaches a halt while the inductive case sets the rules that reduce all successive cases toward the base case. 

Generally, there are two types of recursion: *direct and indirect*. The code we have seen above is direct recursion since the function directly calls itself. Indirect recursion happens when a function \\(f(x)\\) calls \\(g(x)\\) and \\(g(x)\\) calls \\(f(x)\\). Consider this: 

```
function print(int: a):
    print a + " × "
    return a * factorial(a-1)

function factorial(int: a):
    if a == 0 return 1
    print(a)
```
We can extend the category of indirect recursion through the chains of function calls that lead to a loop, that is:

$$g_1 (x_1) \underrightarrow{\ calls\  } g_2(x_2) \underrightarrow{\ calls\  } ... \underrightarrow{\ calls\  } g_1(x_n)$$

Likewise, direct recursion can be stacked on top of each other forming what's called a multiple recursion. The most common example of this is a tree traversal:

```
function preorder(Node: node):
    if node exists:
        print node->value
        preorder(node->right)
        preorder(node->left)
```
---

### Recursion and Iteration
As we have seen earlier, we can express a factorial computation in a recursive manner as well as in an iterative manner. But when should we use one over the other? Let's take a look at the difference between recursion and iteration in performance in terms of time and space complexity, consider running the code below. 

<iframe height="600px" width="100%" src="https://replit.com/@dcode2021/dcodecppseries?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

In the `clang++ std=c++17` compiler for C++17, recursive functions tend to be more performant as it saves more time. However, in a recursive function, the [stack frame](https://en.wikipedia.org/wiki/Call_stack) keeps piling up 8 bytes of memory until it reaches the base case for which the recursive function resolves the value, and only until then, frees the memory allocated in the function's stack frame. Observe:

![Real Python.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1618729597185/BdcyY9Pdk.gif)

It is worth noting that this is generally not the case, some implementation of recursive functions, most notably in imperative languages, tend to be slower than the iterative approach. In functional programming languages, on the other hand, the implementation of a recursive function tends to be much faster than in iteration. However, some compilers have optimized for recursion to reduce the performance overhead. 

Moreover, recursion is a mathematical concept, when we talk about recursion in the context of programming, we are essentially talking about how efficient recursion algorithms are implemented. In a functional paradigm wherein a worldview of seeing everything in the guise of a mathematical function, recursion is natural to the language therein their implementation is more optimized. There are some algorithms that work well with recursion in a functional programming language but become unbearable to compute in some languages. The reason is implementation. 
 
Lucky for us, we can translate *any* recursive programs into an iterative algorithm. This is due to the idea of **Turing equivalence**: two computers \\(P\\) and \\(Q\\) are equivalent, if \\( P \underleftrightarrow{\ simulates\  } Q  \\). And since all Turing complete calculi are strictly equivalent in their expressive power, we can express recursive programs in an iterative approach. Note that this is always possible if we have an infinite amount of memory. 

> I intentionally left the proof of μ-recursion and iteration being Turing complete. If you are interested you can consult the Church-Turing thesis [1] and Beoehm-Jacopini's structured program theorem[2].

This topic ultimately boils down to the context i.e. what language are you using and the manner in how you can conveniently express the solution of your problem.  

---
### Recursion in the Wild

A recursive entity unfolds itself until it reaches a halt, otherwise, infinitely many times. A language can be expressed using an elegant style of recursion. Take for example the grammar of the English language:

```
<Sentence>::= <Noun Phrase> | <Verb Phrase>;
<Noun Phrase>::= <determiner> | <noun>;
<Verb Phrase>::= <verb> | <Noun Phrase>;
```
What we see here is a Backus-Naur Form (BNF) of English grammar. Grammatically speaking, we can generate an infinitely long chain of words forming one sentence. If we substitute the ordering of our BNF above we can construct the following:

```
the dog ate the dog ate the dog ate the dog ate the dog ate the dog ...
```
Three words that unfold in an infinitely long sentence. To me, I find the idea of recursion particularly fascinating in grammars and formal languages for it bears the idea within which we can describe the world in our own languages.  

---
### Links to further readings:

1. Böhm, C., & Jacopini, G. (1966). [Flow diagrams, Turing machines and languages with only two formation rules](https://bityl.co/6Qjt). Communications of the ACM, 9(5), 366-371.
2. Copeland, B. J. (1997). [The Church-Turing thesis](https://bityl.co/6Qju).
3. Dean, Walter, ["Recursive Functions"](https://plato.stanford.edu/archives/spr2021/entries/recursive-functions), The Stanford Encyclopedia of Philosophy (Spring 2021 Edition), Edward N. Zalta (ed.).