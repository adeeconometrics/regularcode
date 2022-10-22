---
title: "Understanding Abstract Data Types"
author: Dave Amiana
date: 2021-08-31
categories: [Programming, Computer Science, C++, Data Structures, Algorithms]
tags: [programming, Computer Science, C++, Data Structures, Algorithms]
math: true
---

This article introduces what we mean by Abstract Data Types (ADT) and its importance in developing our Data Structures Library. Abstraction is of the essence of a scalable software system. In designing systems, we want to work with higher orders of abstraction in the same way we think about concepts. Navigating ourselves in the space of concepts allows us to see the bigger picture and prevents the possibility of inscrutable code -- a result of unorganized ideas. Put it another way, ADT compels us to think more abstractly.

#### What is ADT?

In computer science, an ADT is a mathematical model for certain categories of data structure that have commonalities in their semantics of operations. In practical terms, an ADT serves as a blueprint that _specifies_ the operations to be performed in a data structure. As a result, an ADT does not regard implementation details for it only contain a list of specification. For example, let \\(A\\) be a category of data structures that has `insert()` and `remove()` operations. We can translate this into a list of specifications:

```None
A:
 - insert(int x) - inserts x in a data structure
 - delete(int x) - deletes x in a data structure
```

Notice that an ADT describes the specification from the perspective of the user [1]. We can see that an ADT specifies how an `insert(int x)` and `remove(int x)` should behave. More formally, an ADT is a set of types, a designated type from that type set, a set of functions, and a set of axioms [1].

In OOP, we may create a representation that captures this idea in a form of a contract. Every data structure that belongs in category \\(A\\) must implement the meaning of `remove()` and `delete()` in their respective contexts. That is, \\(A\\) is an abstract concept that contains `insert()` and `delete()` operations.

We can understand this better by looking at an executable program.

```cpp
struct A{
	A() = default;
	virtual ~A() = default;

	virtual void insert(int) = 0;
	virtual void remove(int) = 0;
};

struct B: public A{
	B() = default;
	~B() = default;

	void insert(int x) override {
		std::cout << "implementation of insert in the context of B. \n";
	}
	void remove(int x) override {
		std::cout << "implementation of remove in the context of B. \n";
	}
};

struct C: public A{
	C() = default;
	~C() = default;

	void insert(int x) override {
		std::cout << "implementation of insert in the context of C. \n";
	}

	void remove(int x) override {
		std::cout << "implementation of remove in the context of C. \n";
	}
};
```

The notion of context is well-pronounced in the OOP paradigm which justifies why we are using C++ in this series. The above snippet demonstrates how we may write a contract between classes. Note that structs and classes are the same [except](https://en.cppreference.com/book/intro/classes) that `struct` is public by default while a `class` is private. Since the above code has no private members, I made use of `struct` for convenience.

#### How does ADT help us develop Data Structures?

ADT serves to be our guide in building more abstract concepts. This benefits us from not losing ourselves with the minute details of an ever-growing system of data structures. Mapping our ideas in the space of concepts compels a structure and our responsibility when we implement it is to maintain conceptual consistency.

Maintaining conceptual consistency means that the intention behind operations is deemed appropriate in the context of another data structure. When we remove a value in a list, it should do what we expect it would be: removing an element.

### Summary

ADT helps us navigate in the space of concepts; this puts structure into how we may address problems and develop a category of data structures. ADT is devoid of implementation details, rather it talks about the specification of a data structure.

### References

1. La Rocca, M. (2021). Advanced Algorithms and Data Structures. Manning Publications Co. 20 Baldwin Road.
2. cppreference.com (2021). Structs and Classes. https://en.cppreference.com/book/intro/classes.

---
