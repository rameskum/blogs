---
title: 'Core Java'
date: 2024-05-29T09:50:09-04:00
draft: false
tags:
  - java
  - interview
categories: notes
keywords:
  - java
  - core java
---

## Core Java

- [Core Java](#core-java)
  - [String Concepts](#string-concepts)
  - [HashCode \& Equal Methods](#hashcode--equal-methods)

### String Concepts

A String is a sequence of characters.

**How to create a String object?**

- String literal

  ```java
  String s1="Welcome";
  String s2="Welcome";//It doesn't create a new instance
  ```

- New keyword

  ```java
  String s=new String("Welcome"); //creates object and reference variable
  ```

_Examples:-_

```java
String s1 = "Hello";
char ch[] = {'H', 'e', 'l', 'l', 'o'};
String s2 = new String(ch);
String s3 = "Hello";
String s4 = new String("Hello");
String s5 = new String("Hello");

System.out.println(s1 == s2); // false
System.out.println(s1 == s3); // true
System.out.println(s4 == s5); // false
```

### HashCode & Equal Methods

Default implementations of `equals()` and `hashcode()` methods:

- `equals()` - will return `true` when the reference points to the same memory address.
- `hashcode()` - calculated based on the memory address.

> We can override the `hashcode()` and `equals()` methods, and the contract says both methods should be overridden in such a way that, if the two objects are equal, then the hash-code should be the same as well.
