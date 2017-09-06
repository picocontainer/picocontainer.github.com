---
layout: article
name: Long Constructor Argument List
---

h2. Symptoms

p. The constructor for an object takes a long list of arguments.

h2. Causes

* Can be a result of "Propagating Dependency":/antipatterns/propagating-dependency-antipattern.html .
* Class is concerned with doing more than one thing and therefore has many dependencies.

h2. What To Do

p. If the class is doing too much, it should be refactored into a set of smaller classes. Each class should have a clearly defined responsibility and therefore a smaller set of dependencies.

