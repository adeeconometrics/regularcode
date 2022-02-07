---
title: "World of Forms: An Introduction to Object-Oriented Programming"
subtitle: "Abstraction, Encapsulation, Polymorphism, and Inheritance"
author:
  name: Dave Amiana
date: 2021-04-21
categories: [Computer Science, Programming, OOP]
tags: [Computer Science, Programming, OOP]
math: true
---

Object-oriented programming (OOP) has become one of the most used programming paradigms since its conception. Part of the reason why the object-oriented approach is widely practiced is code-reusability. OOP emphasizes a group of concepts packed up together and their relations with other objects. The main idea behind this paradigm is to *bind* a template for the data and its associated behaviors. 

```cpp
class Object:
        private:
            int int_data;
            string str_data;
        public:
            constructor();
            destructor();
            function get_string();
            function get_int();
            function set_str_data(string _arg);
            function set_int_data(int _arg);
```
The pseudocode above shows some basic features of OOP. We can observe that the object has an internal (`private`) state, as well as the sorts of behaviors it can do externally (`public`). There are two general categories of OOP:  class-based and prototype-based. In this article, we will discuss the differences between class-based, and prototype-based categories. Then we will talk about the core concepts of OOP.

Class-based OOP revolves around the concept that the manner of inheritance among classes happens as one object is combined with another object. In contrast, prototype-based object orientation extends functionality by reusing an existing object of which it serves as a prototype. A notable example of class-based object orientation is Java, while JavaScript is an example of prototype-based object orientation.

Consider implementing a *conceptual model* of an apple. In class-based object-orientation, we would have to group the general properties of fruit since an apple belongs to a kind of fruit. Then we combine these features to a model of an apple class where properties generic to an apple type are implemented. Contrast this with prototype-based object orientation, where we can reuse the resources of our fruit and morph them into a conceptual model of an apple by adding features generic to apples.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619014449404/sXiasFopG.png)

In Java, we write this relationship as:

```java
abstract class Fruits{
    private String name;
    Fruits(String name){this.name = name;}
    //generic method and properties of fruits that are common to all fruits
}

class Apple extends Fruits{
    private String id;
    Apple(String id){this.id = id;}
    //generic method and properties of apple that are specific to all fruits
}
```
In JavaScript, we write this relationship as:

```js
function Fruits(name){
    this.name = name;
    //generic properties of fruits that are common to all fruits
}
// adds generic functions common to all fruits
Fruits.prototype = {
    getName: function(){return this.name;}
}

function Apple(id){
    Fruits.call(this, "Apple");
    this.id = id;
}
// adds generic functions common to all apples
Apple.prototype = {
    getID: function(){return this.id;}
}
//Apple inherits the properties and functions of Fruits
Apple.prototype = new Fruits();

// extending properties of Apple specific to Applies
Apples.prototype.constructor = Apples;
```
Hence prototype-based OOP is otherwise known as class-less OOP. 

---
## The Pillars of Object-Oriented Programming

Object-oriented programming is based on the following concepts:
- Encapsulation
- Abstraction
- Inheritance
- Polymorphism

Let's break this down.

Encapsulation is the process of binding the attributes and methods internal to the object. This concept effectively hides the implementation of some functions specific to an object. It makes sense to summon this concept by private keyword in Java.

Abstraction is picking out the general features among the objects that belong to that category which effectively forms a hierarchy of shared resources. Moreover, it effectively reduces the implementation of a core function for all classes. Without abstraction, your codebase would be filled with boilerplate code; these are blocks of code that are implemented in multiple places with little to no variation. A boilerplate is a significant overhead for development, and abstraction is key to getting rid of them.

Inheritance is the extension of the properties of one class to another. Think of piling up blocks of legos to form a new object. The Legos stacked on top of each other represent how the compiler process inheritance in a class-based OOP: they extend the features of one another. 

Polymorphism is the idea of having multiple implementations to a function. It implies that a function can have many forms based on key arguments or parameter signatures of the function. There are two kinds of polymorphism for compiled languages: Overloading and Overriding. In Java, for example, the Java compiler deduces the type of an overloaded function at compile-time while an overridden function is deduced at runtime. 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619020629122/bv3fEtX5G.png)

---
#### Note:
For all intents and purposes, the JavaScript snippet is made to illustrate how one may implement inheritance. The writer did not keep in mind the ES6 standards which favored classes as a syntax sugar on top of their prototype-oriented system. This was mainly done for ease of use, but one must keep in my the JavaScript still maintained its prototype-based system. For the reader interested to learn about this, here's an interesting article I found on [Hackernoon]("https://hackernoon.com/once-upon-a-time-in-javascript-inheritance-d24m3235).
