---
title: Unique References
author:
  name: Dave Amiana
date: 2021-08-17
categories: [Programming, Computer Science, C++, Ownership Semantics]
tags: [programming, Computer Science, C++, Ownership Semantics]
math: true
mermaid: true
---

Our previous discussion explores the anatomy of pointer types in C and C++ on par with hinting nuances of reference semantics. We discussed the use cases of raw pointers and smart pointers in modern C++ and found that there are degrees of aptness in choosing between automatic resource management over manual resource management. More specifically, we introduced `std::unique_ptr<T>` and demonstrated some of its functionalities. 

This article discusses the design goals and implementation of `std::unique_ptr<T>`. However, instead of discussing the intricacies of the STL implementation in different C++ compiler distributions, we simplify this to express the design goals of a unique reference.

At the end of this article, we develop another layer of understanding with the functionalities of unique reference and implement our version of it. 

### Design goals of unique reference
Let us list down the requirements we must satisfy for implementing our version of the unique reference. Our implementation must satisfy the following.

- Must accept any type
- Automatic resource management
- Maintain the property of uniqueness
- Pointer-like interface

Our simple version of the `std::unique_ptr<T>` requires to be explicitly initialized with the object it refers to. 

### Implementation
Let us build up our implementation by fulfilling our design requirements one by one. 

#### Requirement: Must Accept any Type 

We begin with the simplest requirement *It must take any type*. We simply have to add a template parameter to our type (class) for this requirement to suffice this.
```cpp
template<typename T> class unique_reference;
```
That's it! We satisfied our first requirement. 

#### Requirement: Automatic Resource Management

This is the elegant solution of the C++ abstraction mechanism that comes in: the answer is [*Resource Acquisition Is Initialization*](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization) or RAII.

RAII provides guarantees regarding the state of our class. Resource management is tied with class invariance the lifetime of an object. This is achieved by object constructors and destructors which are responsible for initialization and clean-up of resources.

```cpp
template<typename T> class unique_reference{
T* m_ptr{nullptr};
public:
  unique_reference() = default;

  ~unique_reference() {
    delete m_ptr; // seems right, but dangerous!
    m_ptr = nullptr;
  }
};
```

The constructor is triggered when an instance of `unique_reference` is initialized -- that is why in other languages like  [Python](https://docs.python.org/3/c-api/init_config.html), and  [Swift](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html), these are known as *initializers*. Meanwhile, destructors are triggered when an instance of `unique_reference` goes out of scope. Together, they serve our need for automatic resource management that gives us deterministic behavior which guarantees resource management. The deterministic behavior of our reference management gives superior property over implementing garbage collectors which may or may not clean up a piece of resource at the time an object went out of scope. 

It seems that we managed our resources well. We initialized our pointer to `nullptr` upon construction and de-initialized it by deleting the contents of `m_ptr`. However, there is a subtle element of trouble that we still might leak our resources, even after setting `m_ptr = nullptr`. The compiler will not warn you with this, but you are shooting yourself in the foot. 

To resolve this, we need to re-think our mental model of unique reference. At the time our object goes out of scope we want to destroy the object, not necessarily *delete* them abruptly. So we call the object's destructor `~T()`. 

Note that types that do not have user-provided destructors such as primitives are marked as * [trivial destructors](https://en.cppreference.com/w/cpp/language/destructor) *, which is why `~T()` is valid for [primitives](https://en.cppreference.com/w/cpp/language/types). 

```cpp
template<typename T> class unique_reference{
T* m_ptr{nullptr};
public:
  unique_reference() = default;

  	~unique_reference(){
      if(m_ptr != nullptr){
  	    m_ptr->~T();
        m_ptr = nullptr;
      }
    }
};
```
We're doing great. Let's keep going!

#### Requirement: Maintain Property of Uniqueness
For this, we want our unique reference to be able to transfer its ownership inside a function or a class when need be. By implementing the mechanism of transference, we need to maintain the property of uniqueness. We do so by incorporating  [move semantics](https://bityl.co/8Fgd). There are two parts for enabling moveable ownership, our unique reference has to be: move constructible, and move assignable.

Let us work with our moveable constructors first. 
```cpp
  explicit unique_reference(unique_reference<T> &&other) noexcept {
    std::swap(other.m_ptr, m_ptr);
  }
```
Let us walk through what we are actually saying with the above snippet of code. First, we need to mark our constructors as `explicit` to avoid  [unexpected implicit conversions](https://stackoverflow.com/questions/121162/what-does-the-explicit-keyword-mean). Then we make use of * [rvalue reference](https://bityl.co/8G2V) * for our parameter which marks our class as move constructible. But before we handle move construction, we have to guarantee that upon construction we will not throw exceptions so we mark our `noexcept` specifier.

The body of our function is very trivial to implement. We want to take all the resources of our moved-from reference (`other`), and swap it with our moved-in object (`this`). That said, the second requirement (move assignable) is equally trivial to implement. We simply need to overload the assignment operator.
```cpp
  unique_reference &operator=(unique_reference<T> &&other) noexcept {
    std::swap(other.m_ptr, m_ptr);  
  }
```

We are essentially repeating the body of our assignment and constructor, so for consistency, let us implement a little helper function we declare in private which shall be responsible for swapping the objects.

```cpp
private:
  void swap(unique_reference<T> &other) noexcept {
    std::swap(m_ptr, other.m_ptr);
  }
```
Effectively, this results to:
```cpp
  explicit unique_reference(unique_reference<T> &&other) noexcept {
    other.swap(*this);
  }
  / ... /
  unique_reference&operator=(unique_reference<T> &&other) noexcept {
    other.swap(*this);
  }
```
Since we are discussing the constructors and assignment operators it is a good point to note about how to suppress them and why. The reason we want to suppress constructors are two folds: we want to explicitly mention that we do not intend this to happen, and we want the compiler to check if users try to access such modalities. 

To do this, we simply write:
```cpp
  explicit unique_reference(const unique_reference<T> &Type) = delete;
  unique_reference&operator=(const unique_reference<T> &other) = delete;
```
Here, we suppressed copy-assignment and copy-construction since copying will defy the meaning of being unique. 

#### Requirement: Pointer-like Interface
We need an interface to communicate with the state of our unique reference. For consistency, it has to resemble the interface of a pointer. 

Recall that a pointer can be dereferenced with `*` and `->` operators. And we need `&` operator to inspect the location of our pointer in memory. These are the basic operators we need to overload for our unique reference. To do this, we write:

```cpp
  T &operator*(void) { return *(this->m_ptr); }
  T *operator->(void) { return this->m_ptr; }
  T &operator&(unique_reference<T> &other) { return other.m_ptr; }
```
Let us walk through the three lines.

The first line returns a reference of `*(this->m_ptr)` which means that the content of `m_ptr` is accessed that which we can modify and read. The same idea goes with the arrow operator, we return a pointer to `m_ptr`'s location in memory. The last operator is slightly different in that it returns the address of the pointer and not the referent.  [Recall](https://dcode.hashnode.dev/pointers-and-references-design-goals-and-use-cases)  that a pointer has its own location in memory separate from the entities it points to.

### Putting it all together:

```cpp
template <typename T> class unique_reference {

public:
  T *m_ptr{nullptr};
  explicit unique_reference() = default;
  explicit unique_reference(const unique_reference<T> &Type) = delete;

  explicit unique_reference(T *Type) : m_ptr(Type) {}
  explicit unique_reference(unique_reference<T> &&other) noexcept {
    other.swap(*this);
  }

  unique_reference &operator=(const unique_reference<T> &other) = delete;
  unique_reference &operator=(unique_reference<T> &&other) noexcept {
    other.swap(*this);
  }

  ~unique_reference() {
    if (m_ptr != nullptr) {
      m_ptr->~T();
      m_ptr = nullptr;
    }
  }

  T &operator*(void) { return *(this->m_ptr); }
  T *operator->(void) { return this->m_ptr; }
  T &operator&(unique_reference<T> &other) { return other.m_ptr; }

private:
  void swap(unique_reference<T> &other) noexcept {
    std::swap(m_ptr, other.m_ptr);
  }
};
```
### Test Cases
Time to see if we satisfied our design requirements:

<iframe height="700px" width="100%" src="https://replit.com/@delvinjohn/UniqueReference?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>


### Summary

We fleshed out our design requirements and implemented our version of the unique reference to satisfy what we intend to do with unique references. Upon testing, we found that it sufficiently did what we want it to do: we now have the first piece of our automatic resource management! 

Since that we implemented a simple unique reference class, try to think about how we can extend this for an array-like interface as your homework. Doing this part on your own gives you room to re-think what we did with our `unique_reference`. 

Have fun hacking!

---
### References

- Wikipedia contributors. (2021, May 21). Resource acquisition is initialization. In Wikipedia, The Free Encyclopedia. Retrieved 07:06, August 17, 2021, from https://en.wikipedia.org/w/index.php?title=Resource_acquisition_is_initialization&oldid=1024395395.

- Python (2021). Python Initialization Configuration Reference Manual. https://docs.python.org/3/c-api/init_config.html.

- Swift (2021). Swift Language Guide. https://docs.swift.org/swift-book/LanguageGuide/Initialization.html.

- cppreference.com (2021). Destructors. https://en.cppreference.com/w/cpp/language/destructor.

- cppreference.com (2021). Fundamental types. https://en.cppreference.com/w/cpp/language/types.

- StackOverflow (2010). What is move semantics? https://stackoverflow.com/questions/3106110/what-is-move-semantics.

- StackOverflow (2009). What does the explicit keyword mean? https://stackoverflow.com/questions/121162/what-does-the-explicit-keyword-mean.

- Triangles (2019). C++ rvalue references and move semantics for beginners. https://www.internalpointers.com/post/c-rvalue-references-and-move-semantics-beginners.

- Amiana, D. (2021). Pointers and References: Design Goals and Use Cases. https://dcode.hashnode.dev/pointers-and-references-design-goals-and-use-cases.

