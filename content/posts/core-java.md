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
  - [Immutability](#immutability)
  - [OOPS Concepts](#oops-concepts)

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

### Immutability

Immutability is a software engineering concept stating that an object can not be modified once created. In Java, objects are mutable by default, meaning they can be changed after creation.

To **create an immutable class**, we must do the following:

- Declare the class as `final` so it can't be extended.
- Make all of the fields `private` so that direct access is not allowed.
- Don't provide `setter` methods for variables.
- Make all mutable fields `final` so that a field's value can be assigned only once.
- Initialize all fields using a `constructor` method to perform a deep copy.
- Perform `cloning` of objects in the getter methods to return a copy rather than returning the actual object reference.

```java
public final class ImmutableClassExample {
    private final int id;
    private final String name;
    private final HashMap<String,String> roles;

    public ImmutableClassExample(int id, String name, HashMap<String,String> roles) {
        this.id = id;
        this.name = name;

        HashMap<String,String> rolesCopy = new HashMap<>();
        String key;
        Iterator<String> it = roles.keySet().iterator();
        while (it.hasNext()){
            key = it.next();
            rolesCopy.put(key, roles.get(key));
        }

        this.roles = rolesCopy;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public HashMap<String,String> getRoles() {
        return (HashMap<String,String>) (roles).clone();
    }
}
```

### OOPS Concepts
