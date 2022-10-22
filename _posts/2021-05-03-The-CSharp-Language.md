---
title: "The C# Language: Overview"
author: Dave Amiana
date: 2021-05-03
categories: [Computer Science, Programming, C#]
tags: [Computer Science, Programming, C#]
math: true
---

This article is written for developers who are considering learning C#. I layout the language features and some reasons why you should consider learning the language. This can also serve as a reference point for new developers who want to pick up C# as their first language.

If you are considering C# as your first language, I should note that getting a grip on the language requires a steep learning curve for beginners. That said, it would take time to fully appreciate what C# has to offer. Nevertheless, this rewards the learner with foundational concepts in programming that they can apply quickly with other languages. C# is mainly influenced by an array of languages notably C++, Java, and Rust, the language influenced modern languages such as Dart, Kotlin, and TypeScript [1] to name a few -- that is, these languages share a common set of concepts.

---

C# sought to be a simple general-purpose, object-oriented programming language. The main features of C# are listed as follows:

- [Garbage collection](https://bityl.co/6ebA). Allows automatic resource management, garbage collector reclaims memory occupied by unreachable _unused_ objects.

- [Unified type system](https://bityl.co/6ebB). All entities inherit from `System.Object` -- that includes the primitive types which is a notable feature of C# over other programing languages. This feature increases the system's [interoperability](https://bityl.co/6eTv) with other languages.

- [Exception handling](https://bityl.co/6ebC). This allows programmers to address edge cases and provide useful mechanisms to address unexpected cases.

- [Lambda expressions](https://bityl.co/6ebD). This supports functional programming techniques baked in the language.

- [Nullable types](https://bityl.co/6ebG). This feature allows the compiler to enforce null safety which guarantees that no object references contain null or void values. This improves the security and robustness of C# applications.

- [Asynchronous operations](https://bityl.co/6ebI). This feature enables one to improve their code readability by enabling C# code to read like a sequence of statements while the compiler _actually_ executes the code in a much more complicated manner based on external resource allocations and time stamps for when the operations are completed.

Furthermore, C# supports [imperative](https://bityl.co/6eKo), [declarative](https://bityl.co/6eKq), [functional](https://bityl.co/6eKr), [generic](https://bityl.co/6eKs), and [object-oriented](https://en.wikipedia.org/wiki/Object-oriented_programming) programming.

C# was developed as a part of the `.Net` framework initiative which was later approved as an international standard.

> With .NET, your code and project files look and feel the same no matter which type of app you're building. You have access to the same runtime, API, and language capabilities with each app. ([Microsoft Docs](https://bityl.co/6ebN), 2020)

The `.Net` framework was an initiative taken by Microsoft in 2000, as Oracle bought Java and did not want Microsoft to make changes in its ecosystem. The `.Net` framework is a virtual environment that, like in Java's JVM, offers platform-independent applications. It also allows a wide array of languages to work with the system seamlessly.

Over the years, there have been tremendous changes in C#. Those changes can be seen in the Microsoft Documentation of [C# Version History](https://bityl.co/6eca).

Today, C# is [widely used](https://bityl.co/6ecc) in developing _games_, web applications, desktop applications, and web-services.

---

## A little nudge on the Type system

C# has a unified type system wherein all types share the same set of operations and core features from `System.Object`. There are two kinds of types in C#: _value types_ and _reference types_. We can distinguish them as a variable of a value type _points_ to the value whereas a variable of a reference type refers to an instance of a type. As we see here:

```cs
int x = 3; // primitive types are a value types
Integer y(3); // class types are reference types
```

Value types _copies_ the values whenever you _assign them on_ another variable, whereas reference types point to the same memory location of the variable you assigned to; that is, if we point `y = z` and we change the value of `y`, `z` will also be affected of this change in `y`. Reference types are useful to get rid of copying which can take a significant amount of resources for huge data types.

### Outline of C#'s Type System [3]

- [Value Types](https://bityl.co/6erO)

  - [Simple Types](https://bityl.co/6et7)

    - [Signed Integral](https://bityl.co/6erS) - `sbyte`, `short`, `int`, `long`
    - [Unsigned Integral](https://bityl.co/6erS) - `byte`, `ushort`, `uint`, `ulong`
    - Unicode characters - `char`
    - [IEEE binary floating-point](https://bityl.co/6erU) - `float`, `double`
    - [High-precision decimal floating-point](https://bityl.co/6erV) - `decimal`
    - Boolean - `bool`, represents Boolean values (`true` or `false`).

  - [Enum Types](https://bityl.co/6era) - `enum E {...}` is a type with named constants of the integral types (signed and unsigned).
  - [Struct Types](https://bityl.co/6erb) - `struct S {...}` has a class-like interface for structuring groups of data types together in expressing a coherent concept. Unlike class, a `struct` is value-type meaning, by default, parameter passing makes a copy of data values. A `struct` cannot be a base class of `class` nor can it be extended to other classes by inheritance. `struct` can be instantiated without `new` operator.

  - [Nullable value types](https://bityl.co/6erc) - nullable types are types that is allowed to contain `null` value. We can enforce this with our types by using the syntax: `Type? type_name`, e.g. `bool? flag` means `flag` variable can accept `null` or indeterminate values.

  - [Tuple value types](https://bityl.co/6erg) - `(T1, T2, ...)` provides a convenient way of grouping types together.

- [Reference Types](https://bityl.co/6erh)

  - [Class types](https://bityl.co/6eri) - `class C {...}`.`System.Object` is a class type, hence the ultimate base class of all types are `class` types.

  - [Interface types](https://bityl.co/6erj) - is an entity that enforce abstract functions to class types. This is a user-defined type as `interface I{...}`.

  - [Array Types](https://bityl.co/6erk) - `⟨Type⟩ array[]` is a collection of values in the same type. We can index them in zero-based ordering i.e. `array[0]` points to the first element of the array.

  - [Delegate Types](https://bityl.co/6erl) - `delegate ⟨Type⟩ function(⟨Type and parameters⟩)` _is a reference type that can be used to encapsulate a named or an anonymous method. Delegates are similar to function pointers in C++_ (5).

---

## Hello world in `C#`!

Let's have our first try and feel of the language.

```cs
using System;

class Program{
     public static void Main(string [] args){
          Console.WriteLine("Hello Word!");
    }
}
```

Let's break down our expression.

The first line of our code, `using`, is a command for _importing_ resources to be used by our program. Here we imported the `System` module for accessing a set of classes used for reference data types, exception handling, event handling, etc (4). We used this for our `Console.WriteLine()` command, which simplifies the command `System.Console.WriteLine()`, to print values on our console.

C# is a class-mandatory language, like Java. This organizes the components of our code into objects with related conceptual meaning. So, even if our program does not use [Object-Oriented Programming](https://bityl.co/6est), the compiler enforces that our code is organized in _classes_.

We notice the `public static void Main(string [] args)` as the signature of our `Main` function. What this means is the function can be accessed [publicly](https://bityl.co/6esz) and it does not need the `Program` class to be [instantiated](https://bityl.co/6esy) for us to use the `Main` function. `(string [] args)` are default parameters in case we want our function to accept parameters from the command line. This is stored in an array of strings called `args`.

Let's play with our code so far!

<iframe frameborder="0" width="100%" height="700px" src="https://replit.com/@dcode2021/GettingStarted?lite=true"></iframe>

---

## Final remarks

Programming languages are our tools for crafting our software projects, it certainly helps if we pick up more than one language to be included in our toolbox. Learning a new programming language, like learning human languages, is a matter of experience and getting used to. There tons of helpful documentation and challenges you can find online.

Here are some good resources that can help you get started:

- [Getting Started in C# by Microsoft](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/hello-world)
- [C# Basic by Hackerrank](https://www.hackerrank.com/skills-directory/c_sharp_basic)
- [C# Developer by Hackearth](https://www.hackerearth.com/recruit/resources/how-to-hire/c-sharp-developer/)

_Good luck on your journey. Cheers!_

---

## References:

1. Wikipedia contributors. (2021, May 2). C Sharp (programming language). In Wikipedia, The Free Encyclopedia. Retrieved 03:58, May 3, 2021, from https://en.wikipedia.org/w/index.php?title=C_Sharp_(programming_language)&oldid=1021104235.

2. ECMA International (2017). [C# Language Specification. 5th Edition](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/).

3. Microsoft (2021). [A Tour of the C# Language](https://bityl.co/6eqP). Microsoft Docs.Retrieved May 3, 2021, from https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/.

4. Microsoft (n.d.). [The System Namespace](https://bityl.co/6esZ). Microsoft Docs. Retrieved May 3, 2021, from https://docs.microsoft.com/en-us/dotnet/api/system?view=net-5.0.

5. Microsoft (2019). [Built-in Reference Types (C# reference)](https://bityl.co/6evS). Microsoft Docs. Retrieved May 3, 2021, from https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types.
