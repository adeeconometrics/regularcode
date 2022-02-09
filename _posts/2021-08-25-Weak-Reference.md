---
title: Weak References
subtitle: "let's write our version of `std::weak_ptr<T>`"
author:
  name: Dave Amiana
date: 2021-08-25
categories: [Programming, Computer Science, C++, Ownership Semantics]
tags: [programming, Computer Science, C++, Ownership Semantics]
math: true
---

In our last discussion, we talked about the semantics of sharing. We fleshed out the design goals of shared reference and met the minimum design requirements. Similarly, we do the same with our implementation of weak references. 

If you followed along with the series, we now know that weak references solve the problem of reference cycles; that is, without weak references, shared reference may be misused, resulting in  [reference cycles](https://en.wikipedia.org/wiki/Reference_counting), which results in suppressing resource clean-up.

Once again, let us demonstrate the problem of reference cycles with our `shared_reference<T>` class. 

```cpp
#include "shared_reference.h"
#include <iostream>

struct B;

struct A{
    shared_reference<B> ab;
    A(){ std::cout << "Resources of A are acquired. \n"; }
    ~A(){ std::cout << "Resources of A are cleaned up. \n"; }
};


struct B{
    shared_reference<A> ba;
    B(){ std::cout << "Resources of B are acquired. \n"; }
    ~B(){ std::cout << "Resources of B are cleaned up. \n"; }
};

int main(){
    shared_reference<A> sa (new A());
    shared_reference<B> sb (new B());

    sa->ab=sb;
    sb->ba=sa;
}

```
If we compile the above code on the IDE provided below, we encounter the following output. 
```None
Resources of A are acquired.
Resources of B are acquired.
```
It never released our resources at the time our objects went out of scope. This problem should be solved with a weak reference type.

---
### Design goals
Our weak reference class is an extension of shared reference as it can access and modify the contents of our shared reference without imposing its presence on the `shared_reference` interface. Consequently, we have must satisfy the following requirements:
- Must initialize only `shared_reference<T>` type
- Must not take ownership of `shared_reference<T>` type
- Pointer-like interface
- Must provide a function of counting references 

---
### Implementation
Now that we defined our goals, let us work on them!  


#### Requirement: Must initialize only `shared_reference<T>` type
I found this a little bit tricky to implement. We will find out why it is tricky when we implement a  non-owning mechanism. But for now, let us try to partially fulfill our first requirement. 

```
template<typename T> class weak_reference: public shared_reference<T>{
    public:
        explicit weak_reference(shared_reference<T>& i_ptr);
};
```
This may seem what we meant by an *extension* of shared reference. And by the looks of it, it may be a plausible assumption until we ran into the problem of calling destructors. Let us discuss the subtle design flaws on this matter in our attempt to suffice the second requirement. 


#### Requirement: Must not take ownership of `shared_reference<T>` type

Before we proceed into anything, let us be clear by what we mean by taking ownership? In the context of shared reference, all entities share ownership with the resource it has; that is, for each time a `shared_reference` instance goes out of scope, it determines the state on whether calling the resource clean-up would be appropriate. Now, our weak reference should not take ownership of `shared_reference`'s resource. We only need to be able to access and modify its content. In doing so, we need to communicate with the `shared_reference` class.

To establish communication between classes, we have to make dependencies. That is why our attempts jump back and forth with our [shared_reference](https://dcode.hashnode.dev/implementing-building-blocks-of-reference-semantics-shared-reference) class. Let us consider our first attempt at establishing dependency with inheritance and discover its design flaws. Consider an adjacent example:

```cpp
class SuperType{
public:
	SuperType() { std::cout << "SuperType resources are acquired. \n"; }
	virtual ~SuperType() { std::cout << "SuperType resources are released. \n"; }
};

class Subtype: public SuperType{
public:
	Subtype() { std::cout << "Subtype resources are acquired. " << '\n'; }
	~Subtype() { std::cout << "Subtype resources are released. " << '\n'; }
};

int main() {
    Subtype s;
}
```
At the time `s` goes out of scope, it outputs the following:
```None
SuperType resources are acquired. 
Subtype resources are acquired. 
Subtype resources are released. 
SuperType resources are released. 
``` 
In the above example, we inevitably call the superclass destructor. And we must call its destructor if we want to establish dependency through inheritance, but what does it mean when we decided to extend share reference to weak reference by means for the inheritance? You guessed it! We alter the state of our reference counter and possibly preemptively release our resources because of it.

```cpp
shared_reference<A> sh_ref(new A());
{
    weak_referene<A> wk_ref(sh_ref);
} // since it calls superclass destructor the clean-up happens here

```
Let us step back and consider another attempt of establishing dependency. Recall that our concern is to get access to `shared_reference`'s private members. For this case, we can use a [friend](https://en.cppreference.com/w/cpp/language/friend) to access `shared_reference`'s private members without inheriting them. 

We declare a friend class inside our `shared_reference` as follows:
```cpp
#include "weak_reference.h"

template<typename T> class shared_reference{
  template <typename> friend class weak_reference;
  static size_t m_counter;
  T* m_ptr;
.
.
.
};
```
Then we write a special constructor that accepts `shared_reference` and an internal representation of that reference inside our `weak_reference` class. 
```cpp
# include "shared_reference.h"

template <typename T> class weak_reference{
    T *m_ptr{nullptr};
    shared_reference<T> handle;

  public:
    explicit weak_reference(shared_reference<T>& i_ptr) : 
      m_ptr(i_ptr.get()), 
      handle(i_ptr) 
    {}

    weak_reference() = default;
.
.
.
};
```
Recall that for each time we call the reference constructor, we call the `copy()` function that increments the state of our reference counter. Since we do not want to alter the state of our reference counter, we have to counteract the consequences of calling the reference constructor. Inside our shared reference class, we declare a private function that is responsible for suppressing unintended incrementation by calling the reference constructor. 

```cpp
private:
    void suppress_increment(void) noexcept {m_counter -= 1; }
    void suppress_decrement(void) noexcept {m_coutner += 1; }
```
Since for each time, `shared_reference` destructor gets called we decrement our reference counter until it is set for clean-up, we need to make sure this will not happen in the context of `weak_reference`. So we write another private function for suppressing unintended decrementation. 

In effect, our weak reference will not alter the state of the reference counter and we have got ourselves a representation of shared_reference which we shall exploit later.


#### Requirement: Pointer-like interface

We need an interface to communicate with the state of our unique reference. For consistency, it has to resemble the interface of a pointer. 

Recall that a pointer can be dereferenced with `*` and `->` operators. And we need `&` operator to inspect the location of our pointer in memory. These are the basic operators we need to overload for our unique reference. To do this, we write:

```
    T &operator*(void) { return *(this->m_ptr); }
    T *operator->(void) { return this->m_ptr; }
    T &operator&(weak_reference<T> &other) { return other.m_ptr; }
```
Let us walk through the three lines.

The first line returns a reference of `*(this->m_ptr)` which means that the content of `m_ptr` is accessed that which we can modify and read. The same idea goes with the arrow operator, we return a pointer to `m_ptr`'s location in memory. The last operator is slightly different in that it returns the address of the pointer and not the referent.  [Recall](https://dcode.hashnode.dev/pointers-and-references-design-goals-and-use-cases)  that a pointer has its own location in memory separate from the entities it points to.

Let's go beyond our requirement list and add little features that return the current count of our references and their contents. We call these functions `count()`, `is_expired()`, and `release()`. The implementation is equally trivial.


#### Requirement: Must provide a function of counting references

Since we have an internal representation of shared reference, we can inspect the state of its reference counter and check if our resources have been released. Additionally, we may want our weak reference to release its handle on shared reference resources. These functionalities are handled by the following public functions:

```cpp
    int count(void){ return handle.count(); }
    void release(void) noexcept { m_ptr = nullptr; }
    bool is_expired(void) noexcept { return m_ptr == nullptr; }
```

---
### Putting it all together
```cpp
# pragma once
# include "shared_reference.h"

template <typename T> class weak_reference{
    T *m_ptr{nullptr};
    shared_reference<T> handle;

  public:
    explicit weak_reference(shared_reference<T>& i_ptr) : 
      m_ptr(i_ptr.get()), 
      handle(i_ptr) 
    {i_ptr.suppress_increment();}

    weak_reference() = delete;
    weak_reference(const weak_reference<T> &) = delete;
    weak_reference(weak_reference<T> &&) = delete;
    ~weak_reference() { 
      m_ptr = nullptr;
      handle.suppress_decrement(); 
    }

    weak_reference &operator=(weak_reference<T> &&) = delete;
    weak_reference &operator=(const weak_reference<T> &Type) = delete;

    T &operator*(void) { return *(this->m_ptr); }
    T *operator->(void) { return this->m_ptr; }
    T &operator&(weak_reference<T> &other) { return other.m_ptr; } 

    T *get(void) { return (this->m_ptr); }
    int count(void){return handle.count();}

    void release(void) noexcept { m_ptr = nullptr; }
    bool is_expired(void) noexcept { return m_ptr == nullptr; }
};
```

---
### Test cases
Time to see if we satisfied our design requirements:

<iframe height="700px" width="100%" src="https://replit.com/@delvinjohn/WeakReference?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

In the executable program above, we noticed that weak references solve the reference cycle problem.

---
### Summary
We fleshed out our design requirements and implemented our version of the weak reference to satisfy what we intend to do with it. We extended the capability of shared reference by extending its features with weak reference whereby we successfully solved the reference cycle problem.


Here ends the ownership semantics series! I hope you carry along with you the new things we learned regarding resource management. 

As always, have fun hacking!

---
### References
- Wikipedia contributors. (2021, July 29). Reference counting. In Wikipedia, The Free Encyclopedia. Retrieved 08:48, August 25, 2021, from https://en.wikipedia.org/w/index.php?title=Reference_counting&oldid=1036113247

- Amiana, D. (2021). Implementing Building Blocks of Reference Semantics: Shared Reference. https://dcode.hashnode.dev/implementing-building-blocks-of-reference-semantics-shared-reference

- cppreference.com (2021). Friend. https://en.cppreference.com/w/cpp/language/friend.
