---
title: "Programming for Beginners"
author: Dave Amiana
date: 2021-02-16
categories: [Computer Science, Programming]
tags: [Computer Science, Programming]
math: true
---

Programming is a form of communication as it attempts to convey your idea to a recipient, but you have to be direct and unambiguous with your statements. The languages we use to converse with another person are languages with much ambiguity than the kinds of languages we use to communicate with computers. Humans can understand the context and the manner of delivering a message. Computers, on the other hand, are very pedantic as it requires you have to get into their language and talk with them otherwise they will never understand you.

The idea is simple: **reorganize your thoughts in a language a computer can understand**. But why should you do this? There are two good reasons that I can think of: (1) it is an intellectually engaging activity that improves how you think of a problem; (2) it is a place to preserve your ideas and fiddle around with them.

Computer languages, like mathematics, have a rigid set of rules that we call grammar. It has the property of preserving meaning and structure, and for this reason, we call them formal languages. Unlike natural languages, which we speak with people, formal languages are definite. It is meant to disambiguate and keep their structures intact.

> This is a whole [area of discussion](https://en.wikipedia.org/wiki/Formal_language), so I'll leave this hanging for now.

When I started programming, the very first question I had in mind was to think about what constitutes a programming language and how does it work? I asked my friends in CS the sorts of questions they had when they first started programming, and most of them thought of the applications they can do in a programming language. So let's dive into these questions a little bit:

> A programming language is composed of a set of symbols, rules, and the corresponding meaning of their combinations -- we call this syntax and semantics. Such a combination should be expressive enough in performing an algorithm that is essential for solving a particular problem in a particular domain. Languages that extend on multiple domains may be broadly categorized as general-purpose programming languages.

In other words, a programming language is composed of a set of elementary concepts that extends their support for a particular problem domain -- such a domain tailors the set of applications a programming language supports.

For instance, `SQL` is a domain-specific language that is meant for data management -- it is specialized for that purpose, therein exclusively for database transactions. On the other hand, consider `Python`, a general-purpose programming language as it supports multiple programming paradigms for a broad range of problem domains like numerical analysis, web development, and the things in between.

Let's look at some code snippets from `SQL` and `Python`.

```sql
--- SQL code for searching customers in 'Mexico'
SELECT * FROM Customers WHERE country = 'Mexico';
```

The code snippet above _describes_ an objective which is to find customers from Mexico, whereas the code snippet below _specifies_ how to filter a string from a file assuming that a file is organized into a `tuple` of `(customer, country)`.

```py
# python function for retrieving a match given a file
def find_match(fname:str, country = 'Mexico')->list[tuple]:
    with open(fname, 'r', encoding = 'utf8') as rf:
        customers = rf.read().split('\n')
        match = [(element[0], element[1]) for element in customers
                                          if element[1] == country]
        return match
```

As we can observe, imperative programming in Python specifies the computational steps to get the desired output, where the declarative style of SQL describes the output. Another thing to mention is that Python does support a (functional) declarative style of programming and SQL-like interface with the use of some modules such as [Pandas](http://pandas.pydata.org/). Python does even extend for other features of programming such as _object-oriented and functional programming_, making it a general-purpose language.

Now that we know that there can be different _styles_ of programming, and each of these styles varies per our application domain, let's turn our focus on these styles and some of their noteworthy features.

## What is a programming paradigm?

Programming paradigms are means to organize programming languages based on their features. A programming language can support multiple paradigms like in `Python` -- it supports* object-oriented programming, functional programming, and procedural programming* to name a few. While `SQL` is designed to be declarative. Let's break down these paradigms for a bit.

While the list we discuss here is by no means exhaustive, they are the most common which should be sufficient as we grow familiar with these concepts:

- imperative programming focus on specifying the computational steps to get through a process. Two prominent styles encapsulate the spirit of imperative programming:
  - procedural
  - object-oriented
- declarative programming focus on describing the desired result of a computational process. Two prominent styles capture the essence of declarative programming:
  - functional
  - logic

Let's begin with **imperative programming**. As we have seen earlier, imperative programming tends to specify how a computational process might achieve a goal. Given our language, take Python, for instance, we can combine strings for tuning our (imperative) logic forming a control system for guiding our program in achieving a particular goal. As we can imagine, there are nearly infinite combinations of strings that essentially capture the essence of your process. Because of the broad _design space_, we require organization. That is where procedural programming and object-oriented programming comes in.

Procedural and Object-oriented programming is a means to organize our process. We can think of them as two different worldviews where our code lives. In the lens of procedural programming, we break down the process using their functionalities -- often this is called _functional programming_ but it that can only lead us to confusion for a functional programming language is fundamentally different as we will see later. Here's a snippet of a _procedure_ in Python:

```py
def add(a:int, b:int)->int:
    return a+b
```

In the code above, we take two integers and add them together. The function then returns the sum of our inputs as a result of the process. In object-oriented programming, we think of everything as objects: a collection of attributes and behaviors. Object-oriented programming neatly supports an organization a chunk of code by its concepts which is essentially how we naturally associate concepts. Consider the code below:

```py
class Humans:
      def __init__(self, name:str, age:int) ->None:
            self.name = name
            self.age = age

      def eat(food:str)->None:
            print("eating " + food)
      def sleep()->None:
            print("sleeping")
      def talk()->None:
            print("I'm talking")
      def poop()->None:
            print("I'm pooping")
```

Whereas imperative programming _commands_ for certain actions, a declarative language we _demands_ for an output. We do not care how a process is implemented, instead, we focus on describing our output and getting it right.

Let's introduce a new language called _Haskell_. Haskell is a purely functional programming language, and we'll see what we meant by purely functional in a bit. Consider this code:

```hs
in_range :: Integer->Integer->Bool
in_range min max x =
    let in_lower_bound = min <= x
        in_upper_bound = max<= y
    in
        lower_bound && upper_bound
-- returns true if x is in range
```

> Haskell is a purely functional language, which means that functions generally have no side effects. A distinct construct exists to represent side effects, orthogonal to the type of functions. A pure function can return a side effect that is subsequently executed, modeling the impure functions of other languages [1].

In functional programming, you don't have objects or methods, as we introduced earlier. You only have functions. The idea came from mathematics which makes languages such as Haskell known among mathematicians. The declarative style of functional language allows you to focus on what to solve rather than how to solve it. It stems from the idea that we can break down everything as functions.

Since functional programming has drawn inspiration from mathematics, functions are expected to have no side effects. What are these side effects? In imperative languages such as Python or C++, there can be objects that are left unresolved. I think of them as _ghosts from the future past_ since they live freely in your program. Creeping, undetected. The trouble with this is that they can introduce unforeseen bugs that could ruin your program.

<iframe width="100%" height="600px" src="https://www.youtube.com/embed/miW7N2RsYpI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Moving on with logic programming. Let's introduce another language called **Prolog**. Prolog has drawn inspiration from first-order logic and was designed to be declarative. Let's not worry about what first-order logic is, for now, all we need to know is that prolog has drawn inspiration from (declarative) logic, unlike imperative languages which came from imperative logic which is procedure-oriented.

> let's focus on the declarative style of logic programming

```pl
/*facts: characters and their relationships*/
characters(ted).
characters(marshall).
characters(barney).
characters(lily).
characters(robin).

likes(marshall, lily).
likes(ted, robin).
likes(barney, robin).
```

> In Prolog, program logic is expressed in terms of relations, and computation is initiated by running a query over these relations. Relations and queries are constructed using Prolog's single data type, the term [2].

The above code contains the characters of How I Met Your Mother. We can query from that file that contains the characters and verify their relationships. For instance

```pl
character(chandler).
/*outputs "false."*/
likes(Wholikes, barney)
/*outputs "Wholikes = robin."*/
```

Let's look at one can represent such a relationship in [predicate logic](https://brilliant.org/wiki/predicate-logic/):
$$Likes(x,y) | \ x = \text{barney}, y = \text{robin}$$

If ever you worry about the new languages I keep throwing in, keep in mind that you should only focus on how it was written and some of the inspirations that led to programming syntax for this will give you a sense of navigating the space of programming languages. We will have deeper dives into these topics in the future.

---

## Another way of mapping through the space of programming languages

Another way to categorize programming language is through the lens of compiled or interpreted. A compiled language tends to be much faster than an interpreted language. During compilation, the compiler scans the whole program and translates the whole of it into a machine code that a computer can process. An interpreter, on the other hand, translates one statement of the program into a machine code, one at a time. If you worry about performance, you might look into the language specification either they are compiled or interpreted.

## What makes a language worth it to learn?

Take the words from world-class developers for this:

<iframe width="100%" height="600px" src="https://www.youtube.com/embed/5An1sNznblQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="100%" height="600px" src="https://www.youtube.com/embed/LR8fQiskYII" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="100%" height="600px" src="https://www.youtube.com/embed/V_6l0fNtfK8?t=116" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="100%" height="600px" src="https://www.youtube.com/embed/XlvfHOrF26M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<!--
%[https://www.youtube.com/watch?v=5An1sNznblQ]

%[https://www.youtube.com/watch?v=LR8fQiskYII]

%[https://youtu.be/V_6l0fNtfK8?t=116]

%[https://youtu.be/XlvfHOrF26M] -->

At the end of the day, what keeps you learning and working on language will be based on your own interests. What sorts of things fascinate you? What application domain should you focus on? and what territories should you explore further? As you move towards the direction of your interest, you find a set of ideas that are familiar to you, possibly because you had fiddled with some concepts on functional programming. This insight will be very helpful as you think about the design of your program. So, having a little knowledge of different programming paradigms will be helpful for your intuition.

---

# References:

1. Wikipedia contributors. (2021, April 13). Haskell (programming language). In Wikipedia, The Free Encyclopedia. Retrieved 06:56, April 16, 2021, from https://en.wikipedia.org/w/index.php?title=Haskell_(programming_language)&oldid=1017562573
2. Lloyd, J. W. (1984). Foundations of logic programming. Berlin: Springer-Verlag. ISBN 978-3-540-13299-8.
