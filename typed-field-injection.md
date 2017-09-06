---
layout: article
name: Typed Field Injection
---

### Overview

This is where a component has an empty constructor with dependencies indicated by a on a per component basis with an array of field-types to be injected into. These will be provided automatically by the container after instantiation.

```java
public class Apple {
  private Orange orange;
  private Pear pear;
  private Banana banana;
  // methods 
}
```

### Usage

```java
import static org.picocontainer.injectors.TypedFieldInjection.injectionFieldTypes;
pico = new DefaultPicoContainer(new TypedFieldInjection();
pico.as(injectionFieldTypes(Orange.class, Pear.class, Banana.class)).addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
```

Yes that's right, there's no constructor needed. It means that for a Unit Test, you cannot simply 'new' the class without some magic to populate the dependency fields.

The component factory for this is a class **TypedFieldInjection** . It only handles typed-field injection for components.
