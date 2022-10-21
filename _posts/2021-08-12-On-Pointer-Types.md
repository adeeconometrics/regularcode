---
title: On Pointer Types
author: Dave Amiana
date: 2021-08-12
categories: [Programming, Computer Science, C++, Ownership Semantics]
tags: [programming, Computer Science, C++, Ownership Semantics]
math: true
mermaid: true
---


In this article, I aim to introduce the concept and motivation behind using pointers. There are breeds of C++ developers that only use smart pointers for safety reasons, others that only use raw pointers for performance benefits, and some that use both whenever they provide the utmost benefits. In this article, I aim to highlight the use-cases of pointers so you can decide when to use one. 

---
Conceptually, a pointer is a kind of reference. That is, a pointer refers to a certain block of memory of which acts as an entry point. To access the contents or modify the value of a pointer, we *dereference* it. Many languages have pointer-like features. Our concern revolves around the context of C and C++. Note that all C functions are valid in C++ but the opposite is not true. For this reason, our section on smart pointers is only applicable to C++. 

Pointers are the basic structure to achieve reference semantics. Pointers are handy for naming another entity to be attributed by the contents of the referent entity. A pointer may access and manipulate the contents of the entity that it is *pointed* to, consider:

```
int x = 5;
int *y = &x;
*y = 10; 
```

Pointers are introduced in C for the following reasons:
- Inexpensive parameter passing.
- Allocate memory for new objects on the heap.
- Passing a function reference to a function.
- Iterate over data structures.

The same thing is true in C++. In fact, iterators (a component for iterating over C++ Containers) resemble the interface of a pointer. But C++ has more to offer with a different set of modalities one can express reference semantics. We will focus on the set of reference modalities known as smart pointers. But before that, let us present a compelling reason for using smart pointers by enumerating the problems of raw pointers. 


## Raw Pointers
Raw pointers are low-level types that pertain to a certain block of memory. As mentioned, pointers are entry points to access a value or modify it in a specific location in memory. 

Some languages have restrictions on pointer types to a varying degree. For example,  [Ada](https://www.adacore.com/uploads_gems/03_safe_secure_ada_2005_safe_pointers.pdf) initializes a default value of their pointer types to null and makes a restriction for type conversions involving pointers mostly for type safety. While languages like [Java ](https://www.youtube.com/watch?v=ci1PJexnfNE) have banned the use of low-level pointer types to reduce programming error. 

Therein creeps in the subtle shades of pointers. Now we ask what is the scene of pointer types in C?

In languages like C and C++, pointer types are very much used for low-level programming. Type conversion of pointer types has a varying degree of restrictions.  In C, `void *` types are considered as raw pointer types which can be morphed into any type by an operation called typecasting. 

```cpp
void* function(int& i){return &i; }

int main(){
    int i = 10;
    std::cout << *((int*)function(i));
}
```
The above snippet demonstrates that a `void *` type can be cast into an `int *`, or *any* type in that regard. Albeit not apparent with the above example, this is useful for abstraction which is a matter that we will not talk about here. 

Raw pointers are concrete implementation of the more abstract concept of reference. Unlike Ada, raw pointers in C/C++ are not initialized in a null value, although they can refer to null which is a safer way of using raw pointers: if not initialized with the address of a referent, initialize it with `nullptr` (in C++) and `NULL` (in C). The reason behind this is to achieve well-defined behavior and avoid bugs that can be very difficult to fix, let alone find. 

Raw pointers will not impose anything so misusing or abusing it are very easy to commit. 

### The Dangling Problem:
Let us consider an instance that we are likely to encounter the dangling pointer problem. Let's take a look at allocating and deallocating resources using `new` and `delete` function in C++ and `malloc` (`calloc`) or `free` in C. 
```cpp
int main(){
    // (int*)malloc(sizeof(int)) or (int*)calloc(1, sizeof(int)) in C
    int* i = new int(100); 
    std::cout <<"contends of i: "<<  *i << '\n';
    std::cout <<"address of i: " << &i << '\n';
    // free(i) in C
    delete i;
    std::cout <<"contents of i: " <<*i << '\n';
    std::cout <<"address of i: " << &i << '\n';
}
```
Note that `delete` or `free` functions only *removes* the contents of an entity, in this case, `i`. That is, dereferencing `i` still returns the memory location of `i` before it was deleted. This is problematic for it may introduce undefined behavior: dereferencing to an already freed memory is dangerous. To resolve this, we have to make sure that the `i` references to a well-defined location in memory hence we put `i = nullptr` (in C++) and `i = NULL` (in C) after deleting the contents of `i` to avoid critical mistakes.

```cpp
int main(){
    int* i = new int(100);
    std::cout <<"contends of i: "<<  *i << '\n';
    std::cout <<"address of i: " << &i << '\n';
    delete i;
    i = nullptr;
    std::cout <<"contents of i: " <<*i << '\n';
    std::cout <<"address of i: " << &i << '\n';
}
```

Another problem comes with determining the owning pointer which can result in serious issues from resource leaks, undefined behaviors to security vulnerabilities. Which pointer is responsible for which entity? It is difficult to keep track of multiple references and make sure that at the end of the scope such references are correctly managed. There has to be a clear expression to determine the owning pointer. Because of this, C++ introduced a reference management system that revolves around the idea of  [RAII](https://docs.microsoft.com/en-us/cpp/cpp/object-lifetime-and-resource-management-modern-cpp?view=msvc-160).



## Smart Pointers

From the  [C++ Standard](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#???) :
> Enforcing the lifetime safety profile eliminates leaks. When combined with resource safety provided by RAII, it eliminates the need for “garbage collection” (by generating no garbage). Combine this with enforcement of the type and bounds profiles and you get complete type- and resource-safety, guaranteed by tools.

C++ introduced a set of classes for automatic resource management with very minimal to **zero-cost** performance penalties. Using these set of pointers has the following set of benefits:
- clarity of expressing ownership
- guarantees that resources will not leak 

When appropriate, we should be using smart pointers for clearly expressing our intent with the kind of ownership we want to establish between the pointer and the entity it refers to. We have three smart pointers: `std::unique_ptr<T>`, `std::shared_ptr<T>`, and `std::weak_ptr<T>`. We can access these modalities in the `<memory>` header file. 

Let us walk through and discover what these reference modalities have to offer. For our demonstration let us consider a class named `Entity` defined as:

```cpp
class Entity{
    const char* str = "message from Entity. \n";
    public:
        Entity(){ std::cout << "Resources of Entity are acquired. \n"; }
        ~Entity(){ std::cout << "Resources of Entity is cleaned up. \n"; }
        
        const char* function(void){ return str; }
};
```

### Unique Pointer
Unique pointer maintains that there is only one pointer responsible for a given resource in memory. The owning pointer is responsible for the clean-up and resource management. 

Let us define an instance of `Entity`. 
```cpp
std::unique_ptr<E> e (new Entity());
```
Now, let us consider passing the pointer by reference and with move constructors to see its effect on where `Entity` is destroyed. Passing the unique pointer by reference means that the unique pointer is extended to a function, hence the clean-up happens after the unique pointer goes out of scope from the main function where it has been declared.
```cpp
void f(std::unique_ptr<Entity>& entity){
    std::cout << entity->function();
}

int main(){
    std::unique_ptr<Entity> e(new Entity());
    f(e);
    std::cout << "... \n";
}
```
```bash
Resources of Entity are acquired.
message from Entity.
...
Resources of Entity is cleaned up.
```
Passing the unique pointer with move constructors is transferring ownership to the unique pointer defined in function `f()`. We can notice the behavior has we run the following snippet:
```cpp
void f(std::unique_ptr<Entity> entity){
    std::cout << entity->function();
}

int main(){
    std::unique_ptr<Entity> e(new Entity());
    f(std::move(e));
    std::cout << "... \n";
}
```

```bash
Resources of Entity are acquired.
message from Entity.
Resources of Entity is cleaned up.
...
```
Since we transferred the ownership of `e` to `entity`, the clean-up happened as soon as the entity exits the function. 

### Shared Pointer
The shared pointer has an additional mechanism called reference counting where it keeps track of pointers that request for sharing resources. Each time another pointer asks for shared resources, the reference counter adds one and for every time it goes out of scope it removes one. At the time where the reference count is 0, it calls for the clean-up. We can observe this mechanism with the following code snippet:

```cpp
void f(std::shared_ptr<Entity> entity){
	std::cout<<"ref count: " << entity.use_count() << '\n';
    std::cout << entity->function();
}

int main(){
    std::shared_ptr<Entity> e(new Entity());
    std::cout<<"ref count: " << e.use_count() << '\n';
    f(e);
    std::cout << "f went out of scope. \n";
    std::cout <<"ref count: " <<e.use_count() << '\n';
    std::cout << "... \n";
}
```
```bash
Resources of Entity are acquired.
ref count: 1
ref count: 2
message from Entity.
f went out of scope.
ref count: 1
...
Resources of Entity is cleaned up.
```

When I first encountered this, I thought that is all I need, unique pointers and shared pointers are all there is to it. Until I encountered this problem called **circular references** where the troubles of shared references kick in. It is possible to break the guarantees of the shared pointer by forming a loop of circular references which results in resource leaks.

#### The Circular Reference Problem
A circular reference has the form $A \to B$, $B \to A$. Let us put this into code.

```cpp
struct B;

struct A{
	std::shared_ptr<B> ab;
	A(){ std::cout << "Resources of A are acquired. \n"; }
	~A(){ std::cout << "Resources of A are cleaned up. \n"; }
};


struct B{
	std::shared_ptr<A> ba;
	B(){ std::cout << "Resources of B are acquired. \n"; }
	~B(){ std::cout << "Resources of B are cleaned up. \n"; }
};

int main(){
    std::shared_ptr<A> sa (new A());
    std::shared_ptr<B> sb (new B());
    
	sa->ab=sb;
	sb->ba=sa;
}
```

```bash
Resources of A are acquired.
Resources of B are acquired.
```
Since $A \to B$ and $B \to A$, the reference count will never resolve to zero -- the condition which `std::shared_ptr<T>` does its clean-up. We can observe the reference counts by the`use_count()` function. Let is modify the main function:
```cpp
int main(){
    std::shared_ptr<A> sa (new A());
    std::shared_ptr<B> sb (new B());
    
	sa->ab=sb;
	std::cout << "count of ab: " << sa->ab.use_count() << '\n';
	sb->ba=sa;
	std::cout << "count of ba: " << sb->ba.use_count() << '\n';
}
```
```bash
Resources of A are acquired.
Resources of B are acquired.
count of ab: 2
count of ba: 2
```

**How do we break the curse?** Introducing Weak Pointers.


### Weak Pointer
A weak pointer is a special type of pointer in conjunction with shared pointers. With weak pointers, you can access the contents of the shared pointer without increasing the reference count. This is useful for observing the contents of a shared resource and most notably resolving the problem of circular reference. 

Let us solve the problem of circular reference through weak pointers. This can be achieved by modifying either `A::ab` or `B::ba` pointers. 

```cpp
struct A{
	std::shared_ptr<B> ab;
	A(){ std::cout << "Resources of A are acquired. \n"; }
	~A(){ std::cout << "Resources of A are cleaned up. \n"; }
		
};


struct B{
	std::weak_ptr<A> ba;
	B(){ std::cout << "Resources of B are acquired. \n"; }
	~B(){ std::cout << "Resources of B are cleaned up. \n"; }
};

int main(){
    std::shared_ptr<A> sa (new A());
    std::shared_ptr<B> sb (new B());
    
	sa->ab=sb;
	std::cout << "count of ab: " << sa->ab.use_count() << '\n';
	sb->ba=sa;
	std::cout << "count of ba: " << sb->ba.use_count() << '\n';
}
```
```bash
Resources of A are acquired.
Resources of B are acquired.
count of ab: 2
count of ba: 1
Resources of A are cleaned up.
Resources of B are cleaned up.
```


---
## Summary

C++ does not use automatic reference management by default following their design philosophy of the  [zero-overhead principle](https://bit.ly/2VMyrka). One can enable automatic reference management in C++ by making use of smart pointers defined in the standard library. It gives you resource guarantees and safety. Other languages such as  [Swift](https://bit.ly/3s9LNTI) and  [Rust](https://bit.ly/2U8hlwM)  have adhered to the same concept instead of making sophisticated garbage collectors, they do not produce any garbage at all. This memory management model makes a very compelling case of using smart pointers. But raw pointers still have their brightest moments up to date.

Prefer using pointers for performance-critical loops or when the scope is clear enough to determine which is the owning pointer for a given resource. Raw pointers give you few performance benefits, but it comes with the cost of manually keeping track of your pointers, resource safety becomes your responsibility. 

Prefer using smart pointers for expressing ownership clearly. Smart pointers give you resource guarantees which get rid of your task to make sure that there will be no leaks. 

The  [standard](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#???)  recommends the following:
- Look at pointers: Classify them into non-owners (the default) and owners. Where feasible, replace owners with standard-library resource handles (as in the example above). Alternatively, mark an owner as such using owner from the  [GSL](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-gsl).
- Look for naked `new` and `delete`
- Look for known resource allocating functions returning raw pointers (such as `fopen`, `malloc`, and `strdup`)

---
## References
1. Barnes, J. (2005). Safe Pointers. AdaCore. Retrieved from: https://www.adacore.com/uploads_gems/03_safe_secure_ada_2005_safe_pointers.pdf.
2. Computerphile (2017). Why C is so Influential? Retrieved from: https://www.youtube.com/watch?v=ci1PJexnfNE.
3. Microsoft Docs (2020). Raw Pointers (C++).
4. Microsoft Docs (2020). Smart Pointers (Modern C++).
5. Microsoft Docs (2019). Object lifetime and resource management (RAII). 
6. Stroustrup, B. & Sutter, H. (2021). C++ Core Guidelines. Retrieved from: http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#???
7. Stroustrup, B. (2013). The C++ programming language. Pearson Education.

