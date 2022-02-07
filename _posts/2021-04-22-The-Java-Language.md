---
title: "The Java Language: Overview"
subtitle: "Introduction to Java and helpful resources to get started"
author:
  name: Dave Amiana
date: 2021-04-22
categories: [Computer Science, Programming, Java]
tags: [Computer Science, Programming, Java]
math: true
---

In this article, we will briefly introduce the Java language for beginners. Let's begin with an overview of the language.

Java is a class-based object-oriented programming language that is designed for minimizing software dependencies. In Java, everything *inherits* from an *Object* construct which effectively makes everything in Java an object. For this reason, the Java language has become a good choice for learning Object-Oriented Programming (OOP). 

If you are coming from a C/C++ background, the noteworthy features of the Java language are their garbage collection and their Java Virtual Machine which compiles a Java code to a native bytecode representation that is platform-independent. 

Java gets rid of the pointer and low-level memory management manipulations since it is often seen to have caused bugs and a significant overhead for the programmer's responsibility in maintaining their programs. 

Here is the list of noteworthy features of the Java language:
- Object-Oriented. As we mentioned earlier, all entities in Java are rooted in the `Object` class. In addition, we can only interface with functions through classes. This is to say that Java is designed to implement functions within a class, and by the same token, prevent global variables to creep into our code. 
 
- Java is Portable, Platform Independent, and Architecture-neutral. A Java code is compiled into a bytecode representation that is interpreted by the Java Virtual Machine (JVM). This effectively gets rid of platform-specific implementation of a piece of software whereby it can only run one the same platform configuration of the machine it was compiled from. This is the ultimate selling point of Java and why Java has been used mostly by business people. From the point of view of production, this makes Java development streamlined!
> compile once, run anywhere!

- Simple. Java is designed to be simple, and intuitive. If you take a look at their [Standard Library](https://docs.oracle.com/javase/7/docs/api/), you can notice how they kept their naming consistent. This effectively makes their piece of software intuitive and simple, you get what you expect. 

- Secure. In Java, there is a suite of cryptographic libraries that ensures your programs are cryptographically secure. With this, you don't have to worry about consulting a cryptographer and checking out your implementation of SHA-256 encryption for ensuring its soundness and safety. 

- Robust. Java is designed for performance. While it is no doubt that languages like C++ are still faster and more efficient with memory management, Java is a good spot whenever you want to get rid of all the low-level implementation and still have a high-performing code. Many modern software systems are written in Java precisely for this reason. 

---
Now that we see what Java has to offer for our software needs, let's have a very kind introduction to how we can actually interact with Java. Assuming you have already installed the [Java SDK](https://www.oracle.com/ph/java/technologies/javase/javase-jdk8-downloads.html) specific to your machine. You now install an IDE. Here are a couple of my recommendations:
- [Netbeans](https://netbeans.apache.org/download/index.html)
- [Eclipse](https://www.eclipse.org/eclipseide/)
- [IntelliJ](https://www.jetbrains.com/idea/)
- [Visual Studio](https://visualstudio.microsoft.com/downloads/)

If you want a lightweight environment where you can quickly do some testing of your program, [VS Code](https://code.visualstudio.com/download) is highly recommended. 

There are also web-based JVM platforms where we can run and test our code, like the one we will use in this blog: Repl.it. Before we begin, a couple of reminders: Java code can be compiled either from the terminal or from our IDE, second, our public class must match the name of our file, otherwise, our code would not compile.  

For this simple exercise, I prepared two Java programs we can play with: `Main.java`, and `GetName.java`. We can execute this program through our IDE by clicking the `Run` button, while we can compile this from our `shell` by typing the following code for compiling our `Main.java`:

```bash
javac Main.java
java Main
```

The first command compiles the Java code into a `*.class` file where the JVM can execute. The second command executes our `Main.class` file.

<iframe height="600px" width="150%" src="https://replit.com/@dcode2021/Intro?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

For the second program, we imported a library from `java.util.Scanner` so we can use the `Scanner` class in loading our inputs to the program. Notice the keyword `import`. From the second program, we have our first experience of instantiating a class and accessing a member function of that class using the dot `.` operator. Feel free to fiddle around with it and explore some features as you go on.

Great! 

---

## Places to learn Java

To me, the most effective way of learning is by solving problems.

In this section, I compiled some helpful links that you can visit:

- [Hackerrank](https://www.hackerrank.com/domains/java) 
- [HackerEarth](https://www.hackerearth.com/challenges/competitive/java-project-practice-challenge/)
- [W3C](https://www.w3schools.com/java/)

For technical documentation:
- [The Official Java Documentation](https://docs.oracle.com/en/java/)