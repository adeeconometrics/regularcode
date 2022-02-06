---
title: "Pointers and References: Design Goals and Use Cases"
subtitle: "Other languages were made for banning pointers, are they really terrible?"
author:
  name: Dave Amiana
date: 2021-08-12
categories: [Programming, Computer Science, C++, Ownership Semantics]
tags: [programming, Computer Science, C++, Ownership Semantics]
math: true
mermaid: true
---



At first, it seems that C++ makes things more complicated by introducing yet another layer of abstraction. References seem to encapsulate the same set of functionalities as pointers. Both of these constructs are used to *refer* to another entity as they provide access points into manipulating the contents of the *referent* entity; they are both allocated on the heap. 

With this, we are faced with the following questions: 
1. Are pointers and references the same thing? 
2. Are there any techniques that pointers can only do and vice-versa?
3. When do we favor pointers over references and vice-versa?

By the end of this article, I hope to disentangle these two concepts and aim to give a reason why references deserve their own right for building more robust higher-order concepts. 

---

## Pointers

Pointers are introduced in C. Pointers store the memory address of an entity of type `T` (where `T` is a template parameter that can be resolved to *any* types), as mentioned, it provides an access point for changing the values of the entity it points to. Consider the following snippet:

```cpp
#include <iostream>

int main(){
    int x  = 10; // sets the content of x to 10.
    int *y = &x; // sets the pointer to the address of x
    *y = 5; // changes the content of &x to 5.
    
    std::cout <<  x  <<  '\n'; // outputs to content of x that is 5.
    std::cout << y << '\n'; // outputs the memory address of x.
    std::cout << &x << '\n'; // outputs the memory address of x.
}

```

Pointers are great for making use of indirections that manipulate the contents within a certain block of memory. Let us modify the above code  to demonstrate a series of indirection as follows:

```cpp
#include <iostream>

int main(){
    int x = 10;
    int *y = &x;
    int *z = y; // takes the address of y i.e. &x.
    *z = 6;  // changes the content of &x. 
    
    std::cout << x << '\n';
    std::cout << &x << '\n';
    std::cout << y << '\n';
    std::cout << z << '\n';
}
```
We can extend this by nesting them together:

```cpp
#include <iostream>

int main(){
    int x = 10;
    int *y = &x;
    int **z = &y; // declares a double pointer that is pointed to the address of y.
    **z = 6; // dereferencing of a double pointer to change the content of x.
    
    std::cout << x << '\n';
    std::cout << &x << '\n'; // outputs the address of x.
    std::cout << &y << '\n'; // outputs the address of y.
    std::cout << z << '\n'; // outputs the address of y.
}
```
As we notice, the address of pointers is not the same as the address of its reference. This observation is because pointers have their own identity. Consequently, pointers can be reassigned to another variable more so they can tolerate null values: `NULL` and `nullptr` (prefer using `nullptr` if you are [using pointers](https://www.ibm.com/docs/en/i/7.3?topic=pointers-null)). That is, `int *x = nullptr` is valid and will compile.

More so, pointers can iterate over arrays with pre- and post-increment operators, pre- and post-decrement operators, and subscript operators.

```cpp
#include <iostream>

void iterate(int* a_ptr, size_t a_size){
    for(size_t i = 0; i < a_size; ++i){
        std::cout << a_ptr[i] << " ";
    }
    
    std::cout << '\n' << *(++a_ptr); // 2
    std::cout << '\n' << *(--a_ptr); // 1 
    std::cout << '\n' << *(a_ptr++); // 1
    std::cout << '\n' << *(a_ptr--); // 2
}

int main(){
    int array[5]{1,2,3,4,5};
    int *ptr_arr = array;
    iterate(ptr_arr, 5);
}

```

As mentioned, pointers can be used for allocating values on the heap as well as writing in that location. Let's consider allocating memory for an array block with size `3`:

```cpp
#include <iostream>

void output(int* a_ptr, size_t a_size){
    std::cout << '\n';
    for(size_t i = 0; i < a_size; ++i)
        std::cout << a_ptr[i] << " ";
}

int main(){
    int *array = new int[3];
    // printing uninitialized array
    output(array, 3);
    // writing values on array block
    for(size_t i = 0; i < 3; ++i)
        array[i] = i+1;
    
    output(array, 3);
    
    // deallocates the memory
    delete [] array; 
}

```

### Reminders for using Pointers
- Pointers can be difficult to manage as they can be nested and combined without restrictions, this can result in a complicated piece of code that is hard to maintain. 
- Pointers may leak resources if not properly managed: 
  - ensure clean-ups when pointer types are no longer needed
  - remember to deallocate resources allocated on the heap when they are no longer relevant to your code.
- Prefer using smart pointers: `std::unique_ptr<T> u_ptr`, `std::shared_ptr<T> s_ptr`, and `std::weak_ptr<T> w_ptr` over raw pointers when performance is secondary to safety or finer grain of control are unnecessary. Smart pointers allow you to automate clean-up through [RAII](https://bityl.co/8BOl) with a minimal performance cost, in the case of `u_ptr` there is zero performance overhead. 
- For pointer objects `ptr_obj` or user-defined types, members can be accessed through arrow operator e.g. `ptr_obj-> function()`.

## References

References make things more convenient to express, it provides the necessary set of constraints for expressing *ownership semantics* -- a topic that is beyond our concern, for now. C++ follows the design principle that solutions that adhere to the standards should be easier than the alternatives. 

**Now that we know about pointers, how do we contrast this with references?**

References can be thought of as a  [constant pointer](https://bityl.co/8BUS)  `T const* c_ptr = &obj` and should not be confused with `const T* ref = &obj`: The former allows the contents of the object to be modified but restricts the address to refer to `obj`, the latter reverses the effect and only restricts the `obj` to be modified.

```cpp
    int x = 5, y = 6;
    int *const ptr = &x;
    ptr = &y; // error
```
To modify the content of `x` and set it to `y` we say:
```cpp
    int x = 5, y = 6;
    int *const ptr = &x;
    *ptr = y;
```

The same effect is achieved with references. As a result, references bind to the location of the entity in memory. It allows the contents of an entity to be modified, but it *cannot* be reassigned and must be bound at initialization. Therefore, the reference of an entity assumes the entity of the original variable. 

As mentioned, references can also allocate memory for a single entity on the heap.
```cpp
    int &ref = *(new int(0));
    std::cout << ref <<'\n';
    ref = 3;
    std::cout << ref;
```

### Reminders for using References
- References cannot be assigned to `nullptr`.
- References cannot iterate over arrays.
- Returning references over local variables may cause undefined behavior (dangling references).
- References must be bound to an existing entity. 
- References only allow for one level of indirection.
- References express the intention clearly and concisely. 
- Since C++11 standards, references have been used more often as the elements for move semantics are  [built from the notion of references](https://en.cppreference.com/w/cpp/language/move_constructor)  (`lvalue` and `rvalue` references).


---

## Summary

- **Are pointers and references the same thing?** No.
- **Are there any techniques that pointers can only do and vice-versa?** Pointers are more flexible than references which can make your code complicated if not managed well. `T const* ref` achieves the same behavior of references, except for array referencing which gives us more reason for choosing references over pointers (when appropriate) as its semantics restrict iterating over array-like structures:

```cpp
    int const *r = &*(new int[3]{1,3,4});
    std::cout << r[2] <<'\n';
```
Whereas array indexing with references will result to compiler error:

```cpp
    int &r = *(new int[3]{1,3,4};
    std::cout << r[2] <<'\n';
```

- **When do we favor pointers over references and vice-versa?** Pointer types are generally useful for setting up a separate field of indirection capable of modifying the contents of multiple variables of the same type. Having the same pointer accessing multiple variables may be difficult to trace and reason with. It is preferred to maintain clarity over the convenience of typing one pointer to all variables of the same type. For this reason, making use of references is safer.

Pointers are not really terrible, in fact, they are one of the greatest features we unlock in languages like C/C++, however, their power comes with a cost. 

In conclusion, references are not just syntax sugar to cast `T const*`, it has its own design goals and purpose namely containing the address of declared entities. This improves the clarity of our intent, and in these situations, the C++ compiler is our friend.  

