---
layout: article
name: Dependency Injection
---

p. See Martin Fowlers's "Inversion of Control Containers and the Dependency Injection patten":http://www.martinfowler.com/articles/injection.html article from 2003 for a thorough description. Surely everyone has read this by now?

p. Very quickly: Dependency Injection is where components are given their dependencies through their constructors, methods, or directly into fields. Those components do not get their dependencies themselves, or instantiate them directly. This is very much related to the encompassing design principle "Inversion of Control":inversion-of-control.html .

h2. Different types of Dependency Injection supported by PicoContainer

p. PicoContainer supports multiple ways to specify the injection of dependencies into components. Constructor injection (listed first) is the recommended idiom for PicoContainer. Other types leverage fields and methods. Variations of the method types, can follow a naming convention or be marked with an annotation. Dependencies for those could be populated one by one, or all in one method call. Indeed components could be populated with combinations of Constructor, Method and Field Injection.

|_. *Regular Types* |_. *Description* |
| "Constructor":constructor-injection.html |Where a the constructor of a class is used to pass dependencies into it.|
| "Setter":setter-injection.html |Multiple Setter methods on the class are used to pass dependencies in.|
|Annotated Method|Methods are marked with an annotation to indicate that they should be used for injecting dependencies|
|Field|Fields are injected into for dependencies|
|Annotated Field|Fields are marked with an annotation to indicate that they should be injected into|
|Named Field|Fields of a certain name should be injected into|
|Named Method|If method names match other component names, injection happens|
|Typed Field|Fields of a certain type should be injected into|
|_. *Irregular Types* |_. *Description* |
|Adapting|Adapting Injection finds which types of injection are pertinent to a component and builds an injector that fits it precisely. This is the default type of injection for DefaultPicoContainer|
|Multi|Multiple Injection points in order: Constructor, Annotated Methods and then finally Annotated Fields|
|Composite|As Multi, but an open list of Injection types that makes most sense for your components.|
	
h3. Where Next?

 %(callout)The "Modifying Behaviors":behaviors.html page outlines the adding of behaviors to components% 