---
layout: article
name: TieringPicoContainer
---

TieringPicoContainer is the same as DefaultPicoContainer, except that child containers can only seek to locate unsatisfied injection dependencies for their immediate parent container. Their grandparents are out of bounds for dependency resolution. Example:

```java
MutablePicoContainer grandparent = new TieringPicoContainer(); 
MutablePicoContainer parent = grandparent.makeChildContainer(); 
MutablePicoContainer child = parent.makeChildContainer(); 
grandparent.addComponent(Couch.class); 
child.addComponent(TiredPerson.class); 
TiredPerson tp = child.getComponent(TiredPerson.class); // will fail.
```

A PicoCompositionException will be thrown indicating that there was a unsatisfied dependency. Namely TiredPerson needed a couch, but none were available.