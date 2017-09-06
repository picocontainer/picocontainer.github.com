---
layout: article
name: TransientPicoContainer
---

ImmutablePicoContainer is wrapper mechanisms for a MutablePicoContainer. The resulting reference is not able to accept mutating method invocations by any means.

Here is an example of use:

```java
PicoContainer imPC = new ImmutablePicoContainer(someMutableContainer); // imPC has no addComponent methods nor can it be cast back to MutablePicoContainer
```