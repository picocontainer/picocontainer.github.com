---
layout: article
title: Design Patterns
---

Key Patterns

## Inversion of Control (IoC).

The idea that an application is controlled from the top down

## Separation of Concerns (SoC)

The idea that a class (aspect) should do one job and do it well

## Separation of API (or Interface) from Implementation

The idea that you define and code to work interfaces

## Aspect Oriented Programming (AOP)

Mostly lightweight nowadays where you add a chain of interceptors around a method call that can handle orthogonal concerns

## Component Oriented Programming (COP)

The idea that you decompose your software into components

## Declarative Programming

Where you use a declarative-style language (usually xml) to determine things like component wiring (i.e. your average tomcat config file, generalized)

## Event Based Programming (EBP)

Basically making the inter-object method call asynchronous and encapsulating such a call into some kind of event object that can be queued, modified, etc

## More Thinking

Good Citizen: Least surprise, least paranoia

Authors: Dan North, Aslak Hellesoy

Imagine a software system where there is no need for you to spend your time programming defensively; your objects will be used responsibly, and your methods will always be passed sensible arguments.

This low-friction utopia can be approached by establishing some simple programming rules so that every class acts as a 'good citizen' in the society of classes collaborating at runtime.

This page outlines some rules that we, and others, believe lead to good citizenship. All are aimed at improving clarity, reducing surprise, and promoting basic consistency.

As a good citizen, I...

Keep a consistent state at all times - init() or populate() is a code smell.
Have no static fields or methods
Never expect or return null.
Fail fast - even when constructing.
Am Easy to test- all dependent object I use can be passed to me, often in my constructor (typically as Mock Objects).
Accept dependent object that can easily be substituted with Mock Objects (I don't use Concrete Class Dependency).
Chain multiple constructors to a common place (using this(...)).
Always define hashCode() alongside equals()
Prefer immutable value objects that I can easily throw away.
Have a special value for 'nothing' - e.g. Collections.EMPTY\_SET.
Raise checked exceptions when the caller asked for something unreasonable - e.g. open a non-existent file.
Raise unchecked exceptions when I can't do something reasonable that the caller asked of me - e.g. disk error when reading from an opened file.
Only catch exceptions that can be handled fully.
Only log information that someone needs to see.
Classes that are designed for [Constructor Injection](injection.html) are better citizens than those that are not.
