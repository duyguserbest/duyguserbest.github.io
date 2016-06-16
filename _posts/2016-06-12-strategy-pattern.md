---
layout: post
title:  "Head First Design Patterns: Highlighted Lines: Strategy Pattern"
date:   2016-06-12 09:59:00
tags:   java design patterns
---

* **Design Principle**: Identify the aspects of your application that vary and separate them from what stays the same.

* Take the parts that vary and encapsulate them, so that later you an alter or extend the parts that vary without affecting those that don't.

* All patterns provide a way to let some part of a system vary independently of all other parts.

* We know we want to assign behaviors to the instances.

* **Design Principle**: Program to a interface, not an implementation.

* "Program to an interface" really means "Program to a supertype." which means "the declared type of the variables should be a supertype, usually an abstract class or interface, so that the objects assigned to those variables can be of any concrete implementation of the supertype, which means the class declaring them doesn't have to know about the actual object types!"

* Use an interface to represent each behavior.

* Each implementation of a behavior will implement one of those interfaces.

* **Design Principle**: Favor composition over inheritance.

* Creating systems using composition gives you a lot more flexibility. It lets you change behavior at runtime. 

* The **Strategy Pattern** defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

As an example for the strategy pattern, I implemented rpg characters weapon usage as a behavior.

![UML Diagram]({{ site.url }}/assets/strategy-uml.jpg)

My implementation: <a href="https://github.com/duyguserbest/design-patterns/tree/master/strategy" target="_blank">https://github.com/duyguserbest/design-patterns/tree/master/strategy</a>
