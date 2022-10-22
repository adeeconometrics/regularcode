---
title: The R Language Overview
author: Dave Amiana
date: 2021-05-13
categories: [Programming, Computer Science, R]
tags: [programming]
math: true
mermaid: true
---

R is a functional programming language that took inspiration from Scheme [1]. It was made by statisticians Ross Ihaka, and Robert Gentlemen [2] as they began to seek an alternative implementation of the [S language](https://bityl.co/6lIe). The name of **R** came from the names of its core developer, Robert, and Ross.

Functional programming requires another way of thinking about functions. First-class functions are distinct features in R which means that you can do anything with functions as you can do with **RObjects** -- main data structures of R. As a result, you are allowed to assign functions to variables, store them in _lists_, pass them as arguments, and return them as a result of a function [3]. However, R is not a purely functional language in the sense that it does not require you to write pure functions [3-4]. However, functional programming can still be adapted; this lends to good practice for the problem domain of data analysis [3].

R, like Python, is a dynamically typed programming language which means that data types need not be specified as in `int x` in C.

R is an example of a domain-specific language, it is intended to do advanced statistical analyses and modeling, which makes its name in Data Science and Machine Learning.

This article will cover the core features of R that are sufficient for you to get started. I am new to this language, so let's welcome each other! I will end this with links to my resources in learning this language, hopefully bringing your learning a step further.

---

## Overview of the language

R is mainly used by scientists, statisticians, and analysts for exploring subtle features of their data. _It is an integrated suite of software that supports data manipulation, calculation, and graphical display such as plotting functionalities_ [5].

From the R Core developers, R supports the following features:

1. Effective data handling and storage facility.
2. Supports a suite of operators for calculations on arrays, in particular matrices.
3. R has a large, coherent, integrated collection of intermediate tools for data analysis.
4. R features graphical facilities for data analysis and display either directly at the computer or on hard copy.
5. R has a well-developed, simple, and effective programming language (called ‘S’) which includes conditionals, loops, user-defined recursive functions, and input and output facilities. (Indeed most of the system-supplied functions are themselves written in the S language.)

---

## Data Types in R

| Type      | Example         |
| --------- | --------------- |
| Logical   | `TRUE`, `FALSE` |
| Numeric   | `95.678`        |
| Integer   | `10`            |
| Complex   | `9+3i`          |
| Character | `'a'`           |

---

## Operators in R

| Operators                     | Name                                |
| ----------------------------- | ----------------------------------- |
| `<-`, `->`, `=`, `<<-`, `->>` | Assignment operator                 |
| `<`                           | less than i.e. `x < y`              |
| `>`                           | greater than i.e. `x > y`           |
| `==`                          | equal to i.e. `x == y`              |
| `<=`                          | less than or equal to i.e. `x <= y` |
| `!=`                          | not equal to i.e. `x != y`          |
| `!`                           | not i.e. `!x`                       |
| `&`                           | and i.e. `x & y`                    |
| I                             | or i.e. x I y                       |

---

## Valid Variable names

Variables are constructs that represent the contents of memory. We can access the values of these memory blocks by naming them and referring to them in our expressions e.g. `a <- 1` and we assign the value of `a` to a new variable `b`: `b<-a`.

Here are a couple of rules to name your variables:

- Your variable should **not** begin with a number e.g. `200var`.
- Your variable should only contain letters, numbers, dot, and underscore e.g. `var_2`.
- Your variable should **not** start with underscore e.g. `_var`.
- If your variable starts with a dot (`.`), it should **not** be followed by a number e.g. `.2var`.

As we demonstrated earlier, we bind a value to our named entity by using our assignment operator. By convention `<-` is usually used in practice.

---

## BNF of R Expression

An expression generates a value, that is `a&b` is an expression. Backus-Naur Form (BNF) of grammar succinctly describes all the valid instances of an R expression.

```None
<expr>:= <var> <op> <var> | <op> <var>
<op>:= assingment_operator | < | > | <= | => | != | ! | | | &

```

Here `a|b` indicates that either the form of `a` or `b` is valid. For example,`!a` is a valid instance of this grammar as well as `a!=b`.

---

## Fundamental Data Structures in R

If you have ever been in a data structures course or had used data structures in other languages, R's core data structure might seem a little bit odd. At least this was the case for me when I studied this language two weeks ago. This led me to discover the intentions of writing this language: R is a language designed for scientists, statisticians, and data analysts. Now it makes so much sense why we have the following data structures in R: statistical methods operate in [vector space](https://bityl.co/6orn).

Since R is intended to be used in the domain of statistical analyses, the main underlying structure or R is a vector. That said, R is highly optimized to work with vectors. So let's begin with this structure.

### Vector

A vector is a fundamental data type in R. It **cannot** contain a mixture of data types i.e `c(1, "hello world")`. `c()` is a built-in function that converts an item into a vector.

### Lists

We can think of lists are generic vectors that can contain other objects not only limited to one data type e.g. `a = list("a", 3, c(2,3,5))`. As we can notice, we can even make a list of functions in R. Even further, we can make lists of lists in R.

- `list()` is a list constructor for making a list.
- `a[<index>]` is an example of how we can index a value in a list.

Notice how Lists are constructed from a vector by the function `c()`. This is also the same with other fundamental structures in R.

### Matrices

Matrices are 2-dimensional structures that resemble a table, with rows and columns. Virtually all analytics techniques from simple regression to fancy neural networks operate on tabular data (7). Matrices are just one kind of data structure where we can represent tabular data.

Like vectors, every cell in a matrix has to have the same type. The reason for this is _optimization_: at the bottom of it, all data types are represented by chunks of memory blocks, and having an _array_ of `integers`, for example, would be easier to manipulate because they all have the same byte size. It makes addressing to _n-th_ element much simpler as it eliminates the task of deducing the size of the data types per cell first which is unbearable for numerical computation. In other words, matrices are there to optimize our computational needs.

There are many ways to construct a matrix. We will cover some of the most common functions we can use in R.

- **The Matrix constructor**. Let's consider how we might write this matrix in R:

$$
    \mathbf{A} = \begin{bmatrix}
    0 & -1 & -2 & -3 \\\\
    1 & 0 & -1 & -2 \\\\
    2 & 1 & 0 & -1 \\\\
    \end{bmatrix}
$$

```R
> A = matrix(c(0, 1, 2, -1, 0, 1, -2, -1, 0, -3, -2, -1), nrow=3, ncol=4)
> A
     [,1] [,2] [,3] [,4]
[1,]    0   -1   -2   -3
[2,]    1    0   -1   -2
[3,]    2    1    0   -1
```

- **Matrix Transpose**. Now suppose we want to transpose matrix \\(\mathbf{A} \\), we formally express this in linear algebra as \\( \mathbf{A}^{T}\\); quiet naturally, we express this in R as:

```R
> t(A)
     [,1] [,2] [,3]
[1,]    0    1    2
[2,]   -1    0    1
[3,]   -2   -1    0
[4,]   -3   -2   -1
```

- **Combining Matrices**. But what if we want to add rows to our matrix? Let us define our matrix:
  $$
  \mathbf{B} = \begin{bmatrix}
   1 \\\\
   2 \\\\
   3 \\\\
  \end{bmatrix}
  $$

To bind this on our matrix `A`, we simply write the following lines of code:

```R
> B = matrix(c(1,2,3), nrow = 3, ncol = 1)
> B
     [,1]
[1,]    1
[2,]    2
[3,]    3
> cbind(A,B)
     [,1] [,2] [,3] [,4] [,5]
[1,]    0   -1   -2   -3    1
[2,]    1    0   -1   -2    2
[3,]    2    1    0   -1    3

```

- **The Matrix deconstructor**. Just like how we made our matrix from the `c()` function which effectively forms a vector data structure, we can deconstruct our matrix with the same function. Let's deconstruct matrix \\(\mathbf{A}\\).

```R
> c(B)
[1] 1 2 3
```

### Factors

In statistical analysis, we might want to represent categorical variables in a data structure. In other languages, we might construct them using `enums` and represent categories as constant integers. R has a way of expressing `enums` with simpler implementation -- we call it a Factor.

Let's see how we can construct, and make simple operations in this data structure:

- The Syntax for constructing Factors: `⟨variable_name⟩<-factor(⟨vector⟩)`.

```R
> blood_type = c("A","B", "O", "O", "A", "B", "AB", "AB")
> blood_type
[1] "A"  "B"  "O"  "O"  "A"  "B"  "AB" "AB"
> blood_factor <-factor(blood_type)
> blood_factor
[1] A  B  O  O  A  B  AB AB
Levels: A AB B O
```

To inspect the internal structure of `blood_factor` we enter:

```R
> str(blood_factor)
 Factor w/ 4 levels "A","AB","B","O": 1 3 4 4 1 3 2 2
```

Now, we can see that our categories are represented as integers (just like `enums`).

### Data Frames

We can think of a data frame as the generalization of our matrix: it can contain different data types. Which makes it flexible in working with different [structured](https://dcode.hashnode.dev/data-presentation) data types.

- Constructing Data Frame.

```R
> employee_data <-data.frame(emp_id = c(1:5),
+                            emp_name = c("John", "Rick", "Raymond", "Richard", "Christian"),
+                            emp_salary = c(613.13, 201.5, 701.50, 324.25, 420.15))
> print(employee_data)
  emp_id  emp_name emp_salary
1      1      John     613.13
2      2      Rick     201.50
3      3   Raymond     701.50
4      4   Richard     324.25
5      5 Christian     420.15
```

- Summary of Data Frame.

```R
> summary(employee_data)
     emp_id       emp_name   emp_salary
 Min.   :1   Christian:1   Min.   :201.5
 1st Qu.:2   John     :1   1st Qu.:324.2
 Median :3   Raymond  :1   Median :420.1
 Mean   :3   Richard  :1   Mean   :452.1
 3rd Qu.:4   Rick     :1   3rd Qu.:613.1
 Max.   :5                 Max.   :701.5
```

- Numeric indexing from a data frame.

```R
> employee_data[2]
   emp_name
1      John
2      Rick
3   Raymond
4   Richard
5 Christian

> employee_data[c(1,3)]
  emp_id emp_salary
1      1     613.13
2      2     201.50
3      3     701.50
4      4     324.25
5      5     420.15
```

---

## Control Structures in R

Control structures are building blocks of algorithms. R is a simple language, I will simply give you an example of how to implement these concepts in R as the output themselves are self-evident of its logic.

**Conditional statements**

- An `if` statement is used to test assertions using boolean expressions and handling proper controls once a condition is met i.e. the boolean expression resolves to `true`. Syntax: `if(<boolean_expr>){<sub_expr>}`.

```R
> x<-10L
> if(is.numeric(x)){ print("x is numeric.")}
[1] "x is numeric."
```

- An`if-else` statement is used to test and handle and `if` an assertion is met an on occasions where it fails to satisfy our condition. Syntax: `if(<boolean_expr>){<sub_expr>}else{<sub_expr>}`.

```R
> if(is.character(x)){ print("x is character type.") }
  else{ print("x is not a character type.") }
[1] "x is not a character type."
```

- A`switch` statement is used to test multiple cases. Syntax: `switch(<expression>, <case_1>, <case_2>, ...,<case_n> )`.

```R
> y <- 3
> x <- switch(y, y<-y+1, y<-y+2, y<-y+3)
> print(x)
[1] 6
```

As we noticed, a `case` can call functions.

**Looping statements**

- A`for`-loop is used for iterating elements in an iterable object e.g. `vector`, `list`, or `expressions`. Syntax: `for(<element> in <elements>){<sub_expr>}`.

```R
> for(i in c(1,2,3,4,5)){ print(i) }
[1] 1
[1] 2
[1] 3
[1] 4
[1] 5
> vec <- c(1,2,3,4,5)
> for(i in vec){ print(i) }
[1] 1
[1] 2
[1] 3
[1] 4
[1] 5
```

- A `while`-loop is also used for iterating values, in some occasions it might be more convenient to use over `for`-loops. A programmer must be reminded to use `while`-loops should have a counter variable outside its [scope](https://bityl.co/6ox2), in our case, we declared `i` prior to our `while`. Syntax: `while(<boolean_expr>){<sub_expr>}`

```R
> i <- 4
> while( i > 0){
     print(i)
     i<-i-1
  }
[1] 4
[1] 3
[1] 2
[1] 1
```

- A `repeat` statement achieves the same effect with `while` and `for`. Syntax: `repeat{<sub_expr> <stop_condition>}`.

```R
> x <- 10
> repeat{
     print(x)
     x <- x-1
     if(x < 5){
         break
     }
 }
[1] 10
[1] 9
[1] 8
[1] 7
[1] 6
[1] 5
```

**Break statements**

- `next` - skip the iteration of the loop and proceeds with the next iteration.
- `break` - breaks out of the loop, which effectively terminates it.

Let's consider them in action.

```R
> x <- 10
> repeat{
+     if(x == 6){next}
+     print(x)
+     x <- x-1
+
+     if(x < 5){break}
+ }
[1] 10
[1] 9
[1] 8
[1] 7
```

---

## Constructing user-defined functions in R

- To find out about built-in functions use `help(<function_name>)`, `?<function_name>`, or `example(<function_name>)` command

- Syntax for creating your own function `<f_name> <- function(<params>){<body>}`

```R
> f<-function(x){print(x^2)}
> f(9)
[1] 81
```

---

## Installing and Importing Packages

To install an R package:

```R
intall.packages("⟨package_name⟩")
```

To import package:

```R
library(⟨package_name⟩)
```

---

# Time to start hacking in R!

<iframe height="600px" width="100%" src="https://replit.com/@dcode2021/QuickStart?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

---

## Links to explore further on R

- [R-Tutor](http://www.r-tutor.com/)
- [TutorialsPoint in R](https://www.tutorialspoint.com/r)
- [Hackerrank](https://bityl.co/6oyY)

---

#### Download R here:

- [CRAN distribution](https://cran.stat.upd.edu.ph/?fbclid=IwAR0UpY71fuBijsWgZYbNswfr_unS3Jb_TmqJDCSBI-jUvEKzlRZImOubZ8g)
- [RStudio for IDE](https://bityl.co/6p6G)

---

## References

1. Morandat, F., Hill, B., Osvald, L., Vitek, J. (2012). Evaluating the design of the R language: Objects and Functions for Data Analysis (PDF). ECOOP'12 Proceedings of the 26th European Conference on Object-Oriented Programming.
2. Gentleman, Robert (9 December 2006). Individual Expertise profile of Robert Gentleman.
3. Geyer, C.J. (2021). [Stat 8054 Lecture Notes: R as a Functional Programming Language](https://bityl.co/6lhc)
4. Wickham, H. (2019). Advanced R. CRC Press.
5. R Core Team (2018). An Introduction to R.
6. Stirrup, J., & Ramos, R. O. (2017). Advanced analytics with R and Tableau: Advanced analytics using data classification, unsupervised learning and data visualization.
7. Cady, F. (2017). The data science handbook. John Wiley & Sons.
8. Yau, C. (n.d.). R Tutorial: An Introduction to Statistics. http://www.r-tutor.com/.
