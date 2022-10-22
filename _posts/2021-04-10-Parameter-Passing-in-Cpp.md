---
title: "Parameter Passing in C++"
subtitle: "In many forms of sharing"
author: Dave Amiana
date: 2021-04-10
categories: [Computer Science, C++, Programming]
tags: [Computer Science, C++, Programming]
math: true
---

C++ offers a myriad of configuration that enhances performance. Most of these can be traced with the idea of _pointers and references_. Pointers and references are tools for memory management that allow us to work closely with the hardware. They are beneficial in many ways but using them introduces overhead for the programmer. In other words, using pointers and references adds more responsibility for the programmer as they must assure what values their pointers are referencing towards, whatever happens when the value changes, and what resources will get affected. In large projects, it can be very difficult to keep track of the resources that depend on your function, class, or variable.

To resolve this, the programmer must familiarize themselves with the techniques the language supported for maintaining efficiency and ease of use. After all, pointers and references deserve their own right to be included in a high-performing language like C/C++. In this blog, I'll introduce different types of parameter passing and their use-cases.

Since this topic is closely related to the [scopes of variables](https://docs.microsoft.com/en-us/cpp/cpp/scope-visual-cpp#:~:text=When%20you%20declare%20a%20program,visible%20within%20that%20function%20body.) and stack frames, it is important to note that resources that are local to the function are freed after the function is executed. So, returning references to local resources within a function can be problematic which often causes a compilation error.

Generally, there two means of passing an argument to a function: pass by value, and pass by reference. But the concept of constness in C++ deserves its room for this discussion and we shall see more of that later.

---

#### passing by value `void function(Type _type);`

Passing an argument by value requires the program to copy the value of the type the argument has taken on. The main benefit of this method to the programmer is that they would not have to worry about incurring unintended changes anywhere else in their program. Local changes on the `_type` are ensured to only _live_ within the scope of a function.

Consider the code below:

```cpp
void function(int _a){
   std::cout<< a <<std::endl;
}
int main(){
    int a = 5;
    function(a);
}
```

In the program, the `function` has copied the value of `a`, whenever we make changes on the value of `_a` within the scope of the program, that will not incur changes on the variable `a` that we declared on our main function.

On the other hand, for enormous types, copying can take time. We pay for this extra amount of resources as they take a toll on our performance, and we don't want that. So, what can we do?

---

#### passing by reference `void function(Type *_type)` or `void function(Type &_type)`

Passing by reference allows seamless transaction of resources between functions. By seamless, I meant the program no longer copies the value of `_type`. Instead, it takes note of the memory address of that type and uses it thereafter. The catch is: whenever we make changes on `_type`, it manifests anywhere else on our program within which `_type` is used.

Think of sharing a sheet of paper with your friend, any kind of changes done on that sheet of paper may be irreversible. And that's what we ought to look for since our responsibility extends to keeping track of the functions that share on one piece of resources. In passing by reference, we are allowing our functions to rewrite `_type`.

Consider the code below:

```cpp
void function(int *ptr){
	std::cout<< *(ptr) <<std::endl;
	*ptr += 1;
}

void function(int &a){
	std::cout<< a <<std::endl;
	a += 1;
}

int main(){
	int *ptr = nullptr;
	int a = 5;
	ptr = &a;

	function(ptr);
	function(a);

	std::cout<< a << std::endl;
}
```

The program outputs `5,6,7` as we updated the value of `ptr` and `a` which points to the same memory address where the initial value was `5`. In many situations, we may want to consider the concept of sharing a single piece of resources. For instance, we may want to update `_type` such as keeping a record of time per transaction.

But what if we want to restrict our references in a way that we guarantee that a function cannot change whatever the initial value of that reference? In this situation, we should consider passing an argument by `const reference`.

---

#### passing by `const` reference `void function(const Type &_type)` or `void function(const Type *_type)`

Passing by const reference imposes a restriction that the value of our references is not allowed to be changed. This makes them _read-only_.

Consider the code below:

```cpp
void function(const int *ptr){*ptr += 1;}

void function(const int &a){a += 1;}

int main(){
	int *ptr = nullptr;
	int a = 5;
	ptr = &a;

	function(ptr);
	function(a);

	std::cout<< a << std::endl;
}
```

The program throws a compiler error indicating that references cannot be updated. This type of parameter passing is useful whenever a large `_type` is passed on a function as we want to guarantee that the function cannot update the values of `_type`.

---

# Takeaways

In summary, for small types such as primitive types, passing by value is apt. In situations where we may want to update the values or enormous types, we should consider passing by reference whereas in situations where we want to guarantee that a function cannot change the value of `_type`, passing by const reference impose the rule that `_type` cannot be updated.
