---
title: "Preface: Data Structures"
subtitle: "Programs are composed of algorithms and data structures"
author:
  name: Dave Amiana
date: 2021-08-27
categories: [Programming, Computer Science, C++, Data Structures, Algorithms]
tags: [programming, Computer Science, C++, Data Structures, Algorithms]
math: true
---

## Introduction: Mostly about my Experience

I took this course during my sophomore year in Computer Science. I am in my Junior year and I'm currently hacking on languages which I plan to build a series on the design space of compilers someday (*fingers crossed*). Building a software system like compilers requires you to know a lot about Algorithms and Data Structures, just like anything we encounter in the sea of computation. 

> “Bad programmers worry about the code. Good programmers worry about data structures and their relationships.” - Linus Torvalds

As a student of this course, I can remember the fascination and headaches I went through. But it gave me a proper introduction to programming and allowed me to analyze and reason with programs. Overall, it was worth it. 

Our professor in this course welcomed us with enthusiasm. It was because of her dedication and compassion that we carry this knowledge. At the time of writing, *she passed away*. My understanding of this course is indebted to her and other professors that taught us at our University. 

I made this series to share with you everything that I have learned throughout the course.

## Importance

There are two elementary elements of a program: **Data Structures and Algorithms**. A data structure affects the functional and structural aspects of our program. Understanding the key properties of data structures and algorithms allows us to use them whenever they are appropriate. A program is a response to a particular problem. Algorithms are computational steps into solving a given problem, while data structures are the structural representation of a problem. 

## Outline of the series

In this series, we will discuss data structures by developing one. We will walk through the algorithmic steps into achieving a particular state that we want to maintain in a data structure without formally introducing the design space of algorithms for it deserves a separate discussion on its own. 

This article will serve as the outline of the series. We follow a consistent format:
1. **Introduction** - This section will discuss the concepts behind a data structure and its key properties. This will motivate our design goals and implementation.
2.  **Design goals** - In this section, we will flesh out the design requirements of our data structure which we shall satisfy in our implementation section.
3. **Implementation** - This section translates our design objectives into working code. 
4. **Testing** - This section will translate our design goals into a series of assertions that we test for our working solution.
5. **Tradeoffs** - This section discusses the cost and benefits of a given data structure.
6. **Summary** - This section reviews what we had done. 
7. **Reference** - Citation of references made in the article.

Throughout the series we will discuss the following data structures:
- Array-Based - are linear data structures that can be indexed.
 - Array 
 - Vector
 - Stack
 - Queue
 - Deque
 - Heap
- List-Based - are linear data structures that can only be accessed sequentially.
 - Singly Linked List
 - Doubly Linked List
 - Circular Linked List
 - Stack
 - Queue
 - Deque
 - Circular Queue
- Tree-Based - are non-linear data structures that impose ordering.
 - BST
 - AVL
 - RBT
- Hash-Based - are non-linear data structures that do not impose order.
 - Hash Map
 - Hash Set

## Our Language

Ideally, this course is language-agnostic. Since I wanted to share with you a peek at implementing data structures, we shall put translate our design goals into an executable programming environment so we can interact with what we built. In this series, I will be using C++, but I have included on my  [GitHub repository](https://github.com/adeeconometrics/Algorithms)  on the implementation of data structures in other languages such as Dart, Java, C#, Swift, and Python.

## Schedule
I plan to update this series once a week. 
