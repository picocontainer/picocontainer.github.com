---
layout: article
title: Long Constructor Argument List
---

Symptoms
--------

The constructor for an object takes a long list of arguments.

Causes
------

-   Can be a result of [Propagating Dependency](/antipatterns/propagating-dependency-antipattern.html) .
-   Class is concerned with doing more than one thing and therefore has many dependencies.

What To Do
----------

If the class is doing too much, it should be refactored into a set of smaller classes. Each class should have a clearly defined responsibility and therefore a smaller set of dependencies.
