---
title: "Design Principles of Data Structures Library"
author: Dave Amiana
date: 2021-09-01
categories: [Programming, Computer Science, C++, Data Structures, Algorithms]
tags: [programming, Computer Science, C++, Data Structures, Algorithms]
math: true
---

This article will cover the guiding principle for the design and implementation of our data structures library. Since we form our discussion around C++, most ideas we will talk about came from the design principles of Standard Template Library (STL). Modern libraries have been inspired by the main ideas in STL which was introduced by Alexander Stepanov; It is worth looking at the fundamental notions that guide the development of STL and discuss its design philosophy. 

Most of the content of this article is from A. Stepanov's talk on [STL Design and Its Principles](https://www.youtube.com/watch?v=1-CmNNp5eag&t=3398s) in 2002. STL was an attempt to organize software in the same manner that we organize knowledge base. The starting point of STL revolves around the notion of generic programming which A. Stepanov and D. Musser (1986) introduced as a paradigm that revolves around the *idea of abstracting, efficient concrete algorithms to obtain generic algorithms that can be combined with different data representations*. Generic Programming is about abstracting and classifying algorithms and data structures (Stepanov, 1976).


### Design Principles

A. Stepanov laid down the fundamental principles that drive the development of STL. We will briefly discuss these principles and translate them into phases of development that we can work with; note that our concern in this series is only limited to data structures and their abstract representation.

1. **Organization of useful set of algorithms and data structures**. Since STL is an attempt to organize software components, it took the foundational notion from Computer Science where programs are decomposable to a set of algorithms and data structures. Organizing software components involved finding useful algorithms, implementation of correct and efficient algorithms, and useable classification of algorithms.

2. **Finding the most general representations of algorithms**. This idea came from a realization that algorithms share a set of common properties. These properties are drawn from a theoretical field of math known as abstract algebra (Stepanov discusses these ideas in  [his book](https://bityl.co/8UKt)  for the interested reader). Seeing the most abstract representation of algorithms requires formal specifications that outline its properties and type requirements. 

3. **Using whole-part value semantics for data structures**. Whole-part semantics guide the semantics of data structures: when a data structure is copied, the whole part of the data structure is copied; when the whole is destroyed, all parts are destroyed. The interface of a data structure should provide access to all the necessary information that represents its meaning and preserves its properties.

4. **Using abstractions of addresses as the interface between algorithms and data structures**.  A realization of this principle came in the form of an [iterator](https://dcode.hashnode.dev/iterators-the-link-between-data-structures-and-algorithms): an abstract reference handle that binds data structures and algorithms. 

The above set of principles gave birth to the ideas that revolve around STL: data structures, algorithms, and iterators. Since we concern ourselves with developing data structures, we can only apply the above principles insofar as data structures are concerned. In the implementation of STL, abstract data structures are known as Containers. In this series, I will maintain refer to it as data structures for the following reasons: (1) we are not trying to implement everything that the STL implementation has to offer, and (2) we want to keep the distinction of our implementation from that of STL. We are taking the good ideas behind STL in making our simple library for data structures. 

The phases of our development are regarded as follows:
1. Implement a concrete version of a data structure. 
2. Test implementation.
3. Specify the correct interface for abstraction.
4. Abstract the concrete model to represent the general case.

Each of our development phases is documented in this series.


---
### References

- Gray. A. (2016). Alexander Stepanov: STL and Its Design principles. https://youtu.be/1-CmNNp5eag.
- Musser, D. R. & Stepanov, A. A (1989). Generic Programming. https://bityl.co/8UJW.
- Stepanov, A. (1976). Short History of STL. http://stepanovpapers.com/history%20of%20STL.pdf.
- Amiana, D. (2021). Iterators: The link between Data Structures and Algorithms. https://bityl.co/8UWP.
