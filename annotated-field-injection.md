---
layout: article
title: Annotated Field Injection
---

### Injecting into Annotated Fields

This is where a component has an empty constructor with dependencies indicated by a field annotation and provided automatically by the container after instantiation.

```java
public class Apple { 
	@Inject private Orange orange; 
	@Inject private Pear pear; 
	@Inject private Banana banana; 
	// methods 
}
```

#### Usage

```java
pico = new DefaultPicoContainer(new AnnotatedFieldInjection();
pico.addComponent(Apple.class); // etc
Apple apple = pico.getComponent(Apple.class);
```

With an custom annotation instead of PicoContainer's @Inject

```java
pico = new DefaultPicoContainer(new AnnotatedFieldInjection(MyInjectAnnotaton.class);
pico.addComponent(Apple.class); // etc Apple apple = pico.getComponent(Apple.class);
```

Yes that's right, there's no constructor needed. It means that for a Unit Test, you cannot simply 'new' the class without some magic to populate the dependency fields.

The component factory for this is a class **AnnotatedFieldInjection** . It only handles annotation-field injection for components.

Additionally the default component factory **AdaptiveInjection** can also handle field annotation types, if the @Inject annotation from PicoContainer's code-base is used as the marker for injection. *AdaptiveInjection* will also fall through to constructor injection if there is no recognized `Inject annotation.
