---
layout: article
name: Terminology
---

Also known as Picosaurus

## Overview

These can be arrived at from the definitions.

-   Everything is an object
-   Everything which is not a Contextualized Lookup component nor a domain-specific object is a POJO
-   A Setter Dependency Injection (SDI) component is a JavaBean, and a JavaBean is an SDI component
-   A Contructor Dependency Injection (CDI) Component is not a JavaBean, but is a POJO
-   A JavaBean can be any of the three types of IoC and domain-specific all at the same time
-   Spring, PicoContainer, XWork and other frameworks all support both Components and Data Objects to some extend. Avalon tries real hard to not support Data Objects, but that is nearly impossible.

## Terms

### Object or Instance

An "object"as per the Java Language Specification.

### Class

A "class" as per the Java Language Specification.

### POJO (Plain Old Java Object)

An object that does not use nor support any other kind of component/bean specification. It does not implement any lifecycle or marker interfaces, it does not provide any kind of metadata. Will usually follow the minimum requirements for classifying it as a JavaBean though.

### Javabean / Bean

An object that is a"valid java bean"according to the JavaBean Specification. That is, an object that has a public default constructor, and that supports setting and/or retrieving all or a part of its state using so-called setter and getter methods, which are named getXXX(), setXXX() or isXXX(), where XXX identifies part of its state. The JavaBean specification defines a metadata framework, an event framework, and some other things which are less relevant in this context. A JavaBean may represent both data and functionality.

### Data Object / Entity Bean / Data Bean / PODO

An object that only represents data, but contains no 'logic' or any kind of other functionality (except for perhaps some utility methods that transform the data into some other form, like getXXXasString(). Will often override equals() and hashCode(); will often be serializable. Does not create threads, does not keep references to non-data objects.

### Component

An object that only represents functionality. It is a"unit of logic", that can"do work". In a well-designed application, the work that a component can do is specified in a work interface. Will usually not override equals() or hashCode(). It typically coexists with other components of the same language in the same application or server process.

### Service

One or more components that communicate with others over the wire. Services, laced together for some large stack, may be from different technologies or languages, and share only the specification of the wire protocol. Service implies remoteness and potential unavailability or outage.

### Passive Component

A component that does not use threads (not even indirectly).

### Active Component

A component that uses threads (possibly indirectly through the use of a Thread Pool or Executor component).

### Contextualized Dependancy Lookup Component

A component that has a public default constructor and that usually has its state set up and/or modified through the use of some kind of framework-specific mechanism.

### Setter Injection Component

A component that has a public default constructor and that has its state set up through the use of setter methods.

### Constructor Injection Component

A component that has one or more public constructors and that has its state set up through the arguments provided in one of these constructors.

### Constructor/ Setter Dependency Injection Component

A component that has a public constructor and that has its state set up through the arguments provided in that constructor and through the use of setter methods.

### IoC Component

A component that does not implement any kind of logic to set up its own state, but completely depends on other parties to provide that state to it.

### Non-IoC Component

A component that implements some kind of logic to set up its own state. Examples: a component that reads an XML file; a component that looks up dependencies in JNDI; a component that queries system properties.

### Domain-Specific Object

An object that implements a specification that is less generic than Java itself in its applicability. Example: EJB, Servlets.

### Spring-supported Object

An object that can be 'natively' used in the Spring Framework. Any JavaBean (with some extensions to that specification like"initialization completion"support), any object with a public default constructor that does not require any method calls to set up its state.

### PicoContainer-supported Object

An object that can be 'natively' used in PicoContainer. Any Type-3 component.

### XWork-supported Object

An object that can be 'natively' used in the XWork Framework. Any object with a public default constructor that has its state set up through XXXAware interfaces.

### Fail Fast

An object that fails fast is an object that indicates as early as possible if it is in an inconsistent or unusable state (by throwing a fatal exception). The fastest possible way to FailFast is to do so during instantiation. This can be handled in an elegant way if the class is a GoodCitizen.
