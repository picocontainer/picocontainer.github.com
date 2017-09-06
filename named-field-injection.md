---
layout: article
title: Named Field Injection
---

## Overview

This is where a component has an empty constructor with dependencies indicated by a on a per component basis with an array of field-names to be injected into. These will be provided automatically by the container after instantiation.

```java
public class FruitBasket {
  private Orange orange;
  private Pear pear;
  private Banana banana;
  // methods
}
```

## Usage

```java
import static org.picocontainer.injectors.NamedFieldInjection.injectionFieldNames;
pico = new DefaultPicoContainer(new NamedFieldInjection();
pico.as(injectionFieldNames("orange", "pear", "banana")).addComponent(FruitBasket.class);
// etc
FruitBasket fruitBasket = pico.getComponent(FruitBasket.class);
```

Yes that's right, there's no constructor needed. It means that for a Unit Test, you cannot simply 'new' the class without some magic to populate the dependency fields.

The component factory for this is a class NamedFieldInjection. It only handles named-field injection for components.
