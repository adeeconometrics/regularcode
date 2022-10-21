---
title: Shared References
author: Dave Amiana
date: 2021-08-20
categories: [Programming, Computer Science, C++, Ownership Semantics]
tags: [programming, Computer Science, C++, Ownership Semantics]
math: true
mermaid: true
---


In our previous discussion, we fleshed out the design requirements and took action into servicing them. In the same way, this article aims to flesh out the design requirement for shared reference. 

Shared references make use of a reference counting mechanism that allows resources to be shared in different parts of the program. In some cases, this technique replaces the need for garbage collection as reference counting automatically cleans up the resources at the time it hits 0. 

The implementation of shared references has a performance cost which led implementers of [garbage collection (GC) algorithms](https://bityl.co/8H8r) to weigh their choices in implementing automatic resource management. Dynamic languages such as  [Java](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html),  [C#](https://bityl.co/8H9G), and [Dart](https://mrale.ph/dartvm/) implement more sophisticated GC's to minimize the cost of developing applications. Some languages relied on reference counting techniques like C++ and  [Rust](https://doc.rust-lang.org/alloc/rc/)  for system-level programming. And some languages like  [Python](https://bityl.co/8H9l)  make use of both in combination. We can observe that the decision of choosing a resource management system depends on the intended use of the language. For instance, C++ supported smart pointers for automatic reference management while allowing manual resource management through raw pointers because the language is aimed at servicing problems that exist  [from the level of the silicon to the highest levels of abstraction](https://bityl.co/8HD7)  -- that is why C++ provide resource-safe automation and manual resource management. 

Before we implement our shared reference class, let first us discuss the advantages and disadvantages of implementing reference counting (RC). 

#### Advantages
- RC guarantees that resources are cleaned up as soon as their last reference is destroyed. 
- RC is deterministic through RAII.
- RC is relatively simple to implement over more sophisticated GC algorithms.

#### Disadvantages
- RC may leak resources in case of reference cycles.
- RC has space overhead for reference counting. 
- RC is difficult to deal with in a multithreaded environment since it requires atomicity.

Note that the disadvantages vary in the sophistication of RC implementation. In our last discussion, we showed the problem of reference cycles and resolved this by `std::weak_ptr<T>`. We will review this problem and work through solving the problem of reference cycles by implementing a `weak_reference` class. 

In the meantime, let us focus on implementing our simple model of shared reference.

---
### Design Goals

Let us flesh out our design requirements. Our `shared_reference` class must satisfy the following:
- Must accept any types
- Automatic Resource Management
- Reference counting
- Pointer-like interface

---
### Implementation
Now, we translate the design requirements into code. We will build up from it one by one.

#### Requirement: Must Accept any Type
This is trivial, but let us include it here. To put this into code, we simply make use of template parameters as follows:
```cpp
template <typename T> class shared_reference;
```
Simple right? Let's keep going!

#### Requirement: Automatic Resource Management and Reference Counting

The idea is to make use of RAII --  a programming idiom that gives us guarantees with resource-safe management. We simply need to fill out the resource acquisition upon construction and resource clean-up upon destruction. RAII is achieved through constructor-destructor pairs which are triggered to keep track of the object's lifetime. 

```cpp
template<typename T> class shared_reference{
T* m_ptr{nullptr};
public:
  shared_reference() = default;

   ~shared_reference(){
      if(m_ptr != nullptr){
          m_ptr->~T();
        m_ptr = nullptr;
      }
    }
};
```
That seems, reasonable. We initialized `m_ptr` to null and we successfully cleaned up our resources at the time it went out of scope. But that is what we did with `unique_reference`, right? How is shared reference any different?

The answer lies with the reference counting mechanism. To extend the lifetime of our objects, we have to know when it is appropriate to perform our clean-up. As mentioned, we perform our clean-up in the case where our reference count is 0. Now that we get the idea, let us put this in code.

```cpp
template<typename T> class shared_reference{
  size_t m_counter{1};
  T *m_ptr{nullptr};

public:
  shared_reference() = default; 

  ~shared_reference() {
    m_counter -= 1;
    if (m_counter == 0) {
      m_ptr->~T();
      m_ptr = nullptr;
    }
};
```
The idea is to count how many instances that refer to `m_ptr` are still active. With RAII, we can delegate this role to our constructor; that is, for each time it creates an instance of `shared_reference` it has to increment `m_counter` and for each time it goes out of scope, it has to decrement and check if it is time for clean-up. Let's write a copy constructor and a special constructor that takes in a `T*` parameter.

```cpp
shared_reference() = delete;
explicit shared_reference(T* i_ptr): m_ptr(i_ptr){}
explicit shared_reference(shared_reference<T>& rhs){
  rhs.m_ptr = m_ptr;
  rhs.m_counter = m_counter + 1;
}
```
The first constructor is designed to accommodate the initialization of our reference which takes away the need for our default constructor so we deleted it. We want to tell the compiler that it is not free to deduce the default constructor in the case where we need our class to initialize the user has to explicitly bind the content of its referent.

Our *copy constructor* is not technically a copy-constructor because at the time we pass `shared_reference<T>&` we only want it to **share** the contents of `m_ptr` and increment `m_counter`.  What we want is to provide an interface for *sharing* references. To avoid confusion, I will refer to `shared_reference(shared_reference<T>&)` as a reference constructor.

But if we want to transfer the ownership to another entity and we don't want it to have shared access to it anymore? This is the responsibility of our move constructor. 
```cpp
shared_reference(shared_reference<T>&& rhs) noexcept {
  std::swap(m_ptr, rhs.m_ptr);
  rhs.m_counter = 1;
}
```
Notice that we initialized `rhs.m_counter` to 1 and not swapped the values of `m_counter`, this is because we want to re-initialize the counter when it is transferred.

That seems correct. But we ran into deep trouble when we decided to test this. Let us consider the following assertions:
```
shared_reference<AnyType>ptr_0(new AnyType());
{
shared_reference<AnyType>ptr_1(ptr_0);
  {
    shared_reference<AnyType>ptr_2(ptr_1);
  }
}
```
For each time we instantiate an object, we reserve a representation of our model and record the states of its instances. **But we do not share the states of our instances**. So each time we call our reference constructor, we are actually populating unrelated state changes with our instances. 

> That is a subtle bug, not a feature! 

What we actually want is to have a common ground for communication that updates all the instances of `shared_reference<T>` each time it goes out of scope. So, it seems reasonable to use  [static](https://bityl.co/8JKe) members. Let's add the assignment overloads and debug!

```cpp
template<typename T> class shared_reference{
  static size_t m_counter;
  T* m_ptr;

  public:
    shared_reference() = delete;
    explicit shared_reference(T* i_ptr): m_ptr(i_ptr){}
    explicit shared_reference(shared_reference<T>& rhs) noexcept
      {rhs.copy(*this); }
    explicit shared_reference(shared_reference<T>&& rhs) noexcept 
      {rhs.swap(*this); }  

    ~shared_reference(){
      m_counter -= 1;
      if(m_counter == 0){
        m_ptr->~T();
      }
    }

    T &operator=(shared_reference<T>& rhs)noexcept {rhs.copy(*this); }
    T &operator=(shared_reference<T>&& rhs) noexcept {rhs.swap(*this); }

  private:
    void copy(shared_reference<T>& rhs) noexcept{
      m_counter += 1;
      rhs.m_ptr = m_ptr;
    }
    void swap(shared_reference<T>& rhs) noexcept {
      std::swap(m_ptr, rhs.m_ptr);
    }
};

template<typename T> 
size_t shared_reference<T>::m_counter = 1;
```
We did it! We hit two birds with one stone. Let's work on the pointer-like interface!

#### Requirement: Pointer-like Interface
We need an interface to communicate with the state of our unique reference. For consistency, it has to resemble the interface of a pointer. 

Recall that a pointer can be dereferenced with `*` and `->` operators. And we need `&` operator to inspect the location of our pointer in memory. These are the basic operators we need to overload for our unique reference. To do this, we write:

```cpp
  T &operator*(void) { return *(this->m_ptr); }
  T *operator->(void) { return this->m_ptr; }
  T &operator&(shared_reference<T> &other) { return other.m_ptr; }
```
Let us walk through the three lines.

The first line returns a reference of `*(this->m_ptr)` which means that the content of `m_ptr` is accessed that which we can modify and read. The same idea goes with the arrow operator, we return a pointer to `m_ptr`'s location in memory. The last operator is slightly different in that it returns the address of the pointer and not the referent.  [Recall](https://dcode.hashnode.dev/pointers-and-references-design-goals-and-use-cases)  that a pointer has its own location in memory separate from the entities it points to.

Let's go beyond our requirement list and add little features that return the current count of our references and their contents. We call these functions `count()`, and `get()`. The implementation is equally trivial.

---
### Putting it all together
```cpp
template<typename T> class shared_reference{
  static size_t m_counter;
  T* m_ptr;

  public:
    shared_reference() = delete;
    explicit shared_reference(T* i_ptr): m_ptr(i_ptr){}
    explicit shared_reference(shared_reference<T>& rhs) noexcept
      {rhs.copy(*this); }
    explicit shared_reference(shared_reference<T>&& rhs) noexcept 
      {rhs.swap(*this); }  
    
    ~shared_reference(){
      m_counter -= 1;
      if(m_counter == 0){
        m_ptr->~T();
      }
    }

    T &operator=(shared_reference<T>& rhs)noexcept {rhs.copy(*this); }
    T &operator=(shared_reference<T>&& rhs) noexcept {rhs.swap(*this); }
    T &operator*(void) { return *(this->m_ptr); }
    T *operator->(void) { return this->m_ptr; }
    T &operator&(shared_reference<T> &other) { return other.m_ptr; }
    T *get(void) { return this->m_ptr; } 
    size_t count() const { return m_counter; }

  private:
    void copy(shared_reference<T>& rhs) noexcept{
      m_counter += 1;
      rhs.m_ptr = m_ptr;
    }
    void swap(shared_reference<T>& rhs) noexcept {
      std::swap(m_ptr, rhs.m_ptr);
    }
};

template<typename T> 
size_t shared_reference<T>::m_counter = 1;
```

---
### Test Cases
Time to see if we satisfied our design requirements:

<iframe height="700px" width="100%" src="https://replit.com/@delvinjohn/SharedReference?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

---
### Summary
We fleshed out our design requirements and implemented our version of the shared reference to satisfy what we intend to do with shared references. Upon testing, we found that it sufficiently did what we want it to do: we now have the second piece of our automatic resource management! 

Since we implemented a simple shared resource management, think about how you can extend this to support indexing in an array structure.

Have fun hacking!

---
### References
- Wikipedia contributors. (2021, June 29).  Garbage collection (computer science). https://en.wikipedia.org/w/index.php?title=Garbage_collection_(computer_science).

- Oracle (n.d.). Java Garbage Collection Basics. https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html.

- Microsoft Docs (2020). Background garbage collection. https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/background-gc.

- Mrale.ph (n.d.). Introduction to Dart VM. https://mrale.ph/dartvm/.

- Rust (n.d.). Module alloc::rc. https://doc.rust-lang.org/alloc/rc/.

- cppreference.com (2021). std::shared_ptr. https://en.cppreference.com/w/cpp/memory/shared_ptr.

- Debrie, A. (2021). Python Garbage Collection: What It Is and How it works? https://stackify.com/python-garbage-collection/. 

- Microsoft Docs (2020).  Welcome back to C++ - Modern C++. https://bityl.co/8HD7.  

- cppreference.com (2021). static members. https://en.cppreference.com/w/cpp/language/static.
