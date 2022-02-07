---
title: "Python Typed Annotations"
author:
  name: Dave Amiana
date: 2021-04-12
categories: [Programming, Python]
tags: [Programming, Python]
math: true
---

Python 3.10 just announced their [release](https://docs.python.org/3.10/whatsnew/3.10.html) on April 10 this year. Most of the updates were done to improve the developer experience and maintainability of the codebase. 

We enjoyed the fun of writing Python code for its readable syntax, and user-friendly language design. It was clearly meant for getting rid of type prefix you would normally write in other languages such as C++ or Java. Keep in mind that C++ and Java are compiled languages that necessarily impose a set of grammatical rules for mapping them onto a machine code that you can execute. But Python is a dynamically typed language where the interpreter can infer the type of the entities we are calling out on our function by the concept of [duck typing](https://bityl.co/6LB9) -- *if it walks like a duck and it quacks like a duck, then it must be a duck*. This gives us so much room to focus on the algorithm instead of worrying about an obscure programming syntax in expressing the logic of our code. This is okay for relatively small projects where we could just comment out the type of parameters we expect on our function(or method). But building a maintainable software system with people calls for a better solution than commenting out what their function expects.

Before we talk about typed annotations in Python, let us first appreciate the elegance of type prefix in other languages so we can get a sense of when to annotate our code. Whereas type prefix can introduce syntax overheads, it is built-in the language for a reason: (1) [parsing](https://bityl.co/6LBU), and (2) [type-safety](https://bityl.co/6LBT).  In a way, type prefix guards whatever type our function expects to take in and the type it returns for we can generally think of a function as a black box that accepts and returns something. With an imposed typed restrictions to other compiled languages, it enables us to keep the intentionality of our code, that we can guarantee that it is serving its purpose. So, not only that we can clearly write (and read) what goes in and out of our function, but we can also ensure that our function takes in the right types that we specified.

But how can we adapt this to Python? That's where type annotations come in. 

In the following sections, we will explore some features of Type annotations that Python 3.10 supports. Before we begin, here's how we can annotate a simple function in Python:
```py
# annotated 
def λ(x:int)->float:
     return x*0.1

# unannotated
def λ(x):
     return x*0.1
```
As we see in the annotated version of our `λ` function, it takes an integer value of `x` and returns a value of type `float` whereas the unannotated version may require some guessing into what `λ` function expects. Consider the same function written in C++:
```py
float λ(int x){
    return (float)x*0.1;
}
```
The main benefit we get on type annotations in Python is readability and static analysis. The [PEP 484](https://www.python.org/dev/peps/pep-0484/), on Python 3.5 release specified that although Python may potentially support runtime type checking, Python will remain a dynamic language.  

### What is Static analysis and how can we do that in Python?

Static analysis tools are a way to easily detect vulnerabilities without executing a program. This provides an insight into our code without necessarily executing them which automates code quality maintenance. Static analysis tools also automate finding security problems at an early age. So far so great, but how can we use it on our IDE's?

There are several types of static analysis that exist, and they have different purposes ranging from styling analysis to unused code detection. [Here's an article](https://luminousmen.com/post/python-static-analysis-tools) I found that explores the ecosystem for Python static analysis.


----

Now, let's cover some features of type annotations we can do with Python 3.10. 

#### New type union operator

```py
# in previous versions
def λ(x: Union[int, float])->Union[int, float]:
    return x*2

# in Python 3.10
def λ(x: int | float)-> int | float:
   return x*2

```
The new union operator is accepted as the second arguments to `isinstance()` and `issubclass()` functions as in:
````
>>> isinstance(1, int | str)
True
````

#### Improved TypeAlias Annotation
Type aliasing in Python can now be expressed explicitly. Compare:

````
# in previous versions
StrCache = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
````
 and
````
# in Python 3.10
StrCache: TypeAlias = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
````

For more see: https://docs.python.org/3.10/whatsnew/3.10.html.

----


## Takeaways

While type annotations may introduce syntactical overhead, it improves the readability of our code thereby keeping our intentionality intact. As developers, we want to make our codebase easy to use and difficult to misuse. 