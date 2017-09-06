---
layout: article
title: Arrays, Collections and Maps
---

Authors: Aslak Hellesøy and Jörg Schaible

## Supported Collective Types

PicoContainer supports injection of collective types. These are native Arrays, Collections and Maps. Components depending on types implementing these interfaces can be provided automatically with such an instance. Since for native arrays the type can be determined at runtime, this can be done quite automatically, for the other types a special parameter must be provided. For the examples we use following classes:

```java
public interface Fish {
} 
public class Cod implements Fish { 
} 
public class Shark implements Fish { 
}
```

### Arrays

PicoContainer can create a native array of components of a specific type automatically. Example code for the Bowl class in use:

```java
public static class Bowl {
  private final Fish[] fishes;
  private final Cod[] cods;

  public Bowl(Fish[] fishes, Cod[] cods) {
    this.fishes = fishes; 
    this.cods = cods;
  }

  public Fish[] getFishes() { 
    return fishes;
  }

  public Cod[] getCods() { 
    return cods; 
  } 
}
```

Example usage:

```java
pico.addComponent(Shark.class);
pico.addComponent(Cod.class);
pico.addComponent(Bowl.class); 
Bowl bowl = pico.getComponent(Bowl.class);
```

PicoContainer will instantiate the arrays and populate them with all components that matches the array type. Behind the scenes something similar to the following is happening:

```java
Shark shark = new Shark(); 
Cod cod = new Cod(); 
Fish[] fishes = new Fish[] {shark, cod}; 
Cod[] cods = new Cod[] {cod}; 
Bowl bowl = new Bowl(fishes, cods);
```

### Collections

PicoContainer supports automatically generated Collection types. Example code for the Bowl class in use:

```java
public static class Bowl {
  private final LinkedList fishes;
  private final Collection cods;
  public Bowl(LinkedList fishes, Collection cods) {
    this.fishes = fishes; this.cods = cods;
  }
  public Collection getFishes() { 
    return fishes;
  }
  public Collection getCods() { 
	return cods; 
  } 
}
```

Unfortunately for plain collections, there is no way of detecting the type of the components being part of the collection as it is done for native arrays. Therefore you must use a special constructor of [ComponentParameter](/javadoc/core/org/picocontainer/parameters/ComponentParameter.html) and define the component's type:

```java
pico.addComponent(Shark.class);
pico.addComponent(Cod.class);
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(Fish.class, false), new ComponentParameter(Cod.class, false)});
```

The boolean argument defines, that the Collection cannot be empty. Also look at the constructor of the Bowl class. You can use a specific class implementing the Collection interface or just the interface itself and PicoContainer will provide a matching Collection instance.

### Generic Collections

Unlike for plain collections, PicoContainer can automatically detect the type of generic collection as a component dependency. It handles much the same was as for native arrays. i.e. Just use PicoContainer.addComponent() as normal and it will work out what dependencies can be injected for a component like:

```java
public static class GenericBowl {
  private final Listfishes;
  private final Collectioncods;

  public GenericBowl(Listfishes, Collectioncods) {
    this.fishes = fishes; this.cods = cods;
  }

  public List getFishes() { 
	return fishes;
  }

  public Collection getCods() { 
	return cods; 
  } 
}
```

### Maps

PicoContainer also supports automatically generated Map types. Example code for the Bowl class in this case:

```java
public static class Bowl {
  private final TreeMap fishes;
  private final Map cods;

  public Bowl(TreeMap fishes, Map cods) {
    this.fishes = fishes; this.cods = cods;
  }

  public Map getFishes() { 
	return fishes;
  }

  public Map getCods() { 
    return cods; 
  } 
}
```

As for plain collection types, PicoContainer cannot detect the type of the components, that should be part of the collection on its own. Again you must use a special constructor of [ComponentParameter](/javadoc/core/org/picocontainer/parameters/ComponentParameter.html) and define the component's type:

```java
pico.addComponent("Shark", Shark.class);
pico.addComponent("Cod", Cod.class);
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(Fish.class, false), new ComponentParameter(Cod.class, false) });
```

The boolean argument defines, that the Map cannot be empty. Also look at the constructor of the Bowl class. You can use a specific class implementing the Map interface or just an interface itself and PicoContainer will provide a matching Map instance. A special feature is available due to the nature of the Map. The component adapter's key is used also as key in the injected Map.

Use Cases
---------

While the usage of this collective types is straight forward, there are some special cases to consider. These special use cases are explained here.

### Empty Collective Instances

Normally the dependency resolution for a collective type will fail, if no component of the specific component type is registered in the PicoContainer. With the constructors of ComponentParameter you have also the possibility to accept an empty collective type as a satisfying argument. Example code for an Array:

```java
Parameter parameter = CollectionComponentParameter.ARRAY_ALLOW_EMPTY;
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{parameter, parameter}); Bowl bowl = (Bowl) pico.getComponent(Bowl.class);
```

Example code for a Collection (Map is analogous):

```java
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(Fish.class, true), new ComponentParameter(Cod.class, true)}); 
Bowl bowl = pico.getComponent(Bowl.class);
```

Note that in both examples no other component was registered. This behavior is useful if you have a monitor with listeners, that can be registered by configuration. Even if no listener is configured, the monitor component is still instantiable.

### Precedence

PicoContainer will only generate a collective type on the fly, if no such type was registered before. So you can overwrite the dependency resolution (see example for a native Array):

```java
pico.addComponent(Shark.class);
pico.addComponent(Cod.class);
pico.addComponent(Bowl.class);
pico.addComponent(new Fish[]{}); 
Bowl bowl = pico.getComponent(Bowl.class);
```

Demonstrated with this unit test:

```java
List cods = Arrays.asList(bowl.getCods()); 
assertEquals(1, cods.size()); 
List fishes = Arrays.asList(bowl.getFishes()); 
assertEquals(0, fishes.size());
```

See same example code for a Collection (Map is again analogous):

```java
final Set set = new HashSet();
pico.addComponent(Shark.class);
pico.addComponent(Cod.class);
pico.addComponent(Bowl.class, Bowl.class, 
  new Parameter[]{ new ComponentParameter(Fish.class, false), new ComponentParameter(Cod.class, false)});
pico.addComponent(set); Bowl bowl = pico.getComponent(Bowl.class);
Collection cods = bowl.getCods(); 
assertEquals(0, cods.size()); 
assertSame(set, cods); 
Collection fishes = bowl.getFishes(); 
assertEquals(2, fishes.size());
```

But how can you circumvent such a situation and ensure that the collective type is generated even if one of the same type was registered? Make usage of the [CollectionComponentParameter](/javadoc/core/org/picocontainer/parameters/CollectionComponentParameter.html) . Example code for an Array:

```java
pico.addComponent(Shark.class);
pico.addComponent(Cod.class); 
Parameter parameter = new CollectionComponentParameter();
pico.addComponent(Bowl.class, 
Bowl.class, new Parameter[]{parameter, parameter});
pico.addComponent(new Fish[]{});
pico.addComponent(new Cod[]{}); 
Bowl bowl = pico.getComponent(Bowl.class);
```

And here the example code for a Collection (Map is still analogous):

```java
pico.addComponent(Shark.class);
pico.addComponent(Cod.class);
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ 
      new CollectionComponentParameter(Fish.class, false), 
      new CollectionComponentParameter(Cod.class, false)}); // This component will match both arguments of Bowl's constructor 
pico.addComponent(new LinkedList()); 
Bowl bowl = pico.getComponent(Bowl.class);
```

### Scope

Any collective types will collect its components from the complete container hierarchy. See example code for Map types as a unit test:

```java
MutablePicoContainer parent = new DefaultPicoContainer();
parent.addComponent("Tom", Cod.class);
parent.addComponent("Harry", Cod.class); 
MutablePicoContainer child = new DefaultPicoContainer(parent); 
child.addComponent("Dick", Cod.class); 
child.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(Fish.class, false), new ComponentParameter(Cod.class, false) }); 
Bowl bowl = child.getComponent(Bowl.class); 
assertEquals(3, bowl.fishes.size()); 
assertEquals(3, bowl.cods.size());
```

All Cods have been found and put into the bowl. If two components are registered with the same key, the first found will be considered. This is even true, if it means that the component is not part of the collection. See example code again as unit test:

```java
MutablePicoContainer parent = new DefaultPicoContainer();
parent.addComponent("Tom", Cod.class);
parent.addComponent("Dick", Cod.class);
parent.addComponent("Harry", Cod.class); 
MutablePicoContainer child = new DefaultPicoContainer(parent); 
child.addComponent("Dick", Shark.class); 
child.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(Fish.class, false), new ComponentParameter(Cod.class, false) }); 
Bowl bowl = child.getComponent(Bowl.class); 
assertEquals(3, bowl.fishes.size()); 
assertEquals(2, bowl.cods.size());
```

*Dick the Shark* took precedence over *Dick the Cod* .

### Filter based on Key Type

A generated map automatically deliver the component adapter's key as key of the map entry. As for the generic types in Java 5.0 this key may be of a specific type. Just define it using the constructors of ComponentParameter. See the example code:

```java
pico.addComponent(Shark.class);
pico.addComponent("Nemo", Cod.class);
pico.addComponent(Bowl.class, Bowl.class, 
    new Parameter[]{ new ComponentParameter(String.class, Fish.class, false), new ComponentParameter(Cod.class, false) }); 
Bowl bowl = pico.getComponentOfType(Bowl.class);
```

The unit test demonstrates that only **named** fishes are in the fish bowl:

```java
Cod cod = (Cod) pico.getComponentOfType(Cod.class);
Map fishMap = bowl.getFishes();
Map codMap = bowl.getCods(); 
assertEquals(1, fishMap.size()); 
assertEquals(1, codMap.size()); 
assertEquals(fishMap, codMap); 
assertSame(cod,fishMap.get("Nemo"));
```

But this key type selection does not only work with Map types, but also with Collection types and even with a native Array! See the unit test for the Array (although the ValueType parameter will be ignored for a native Array):

```java
List fishes = Arrays.asList(bowl.getFishes()); 
List cods = Arrays.asList(bowl.getCods()); 
assertEquals(1, fishes.size()); 
assertEquals(1, cods.size()); 
assertEquals(fishes, cods);
```

### Individual Filter

Although filtering on component type or key type is useful, it might not be enough. Therefore you can add an additional and completely individual filter by overloading [CollectionComponentParameter.evaluate(ComponentAdapter)](/javadoc/core/org/picocontainer/parameters/CollectionComponentParameter.html#evaluate(org.picocontainer.ComponentAdapter)) . See the example code to filter out the Cod named *Tom* :

```java
pico = new DefaultPicoContainer();
pico.addComponent("Tom", Cod.class);
pico.addComponent("Dick", Cod.class);
pico.addComponent("Harry", Cod.class);
pico.addComponent("Sharky", Shark.class);
pico.addComponent(Bowl.class, Bowl.class, new Parameter[]{ 
	new CollectionComponentParameter(Fish.class, false), 
	new CollectionComponentParameter(Cod.class, false) { 
		protected boolean evaluate(ComponentAdapter adapter) { 
			return !"Tom".equals(adapter.getComponentKey()); 
		} 
	} 
}); 
Cod tom = pico.getComponent("Tom"); 
Bowl bowl = pico.getComponent(Bowl.class); 
assertTrue(bowl.fishes.values().contains(tom)); 
assertFalse(bowl.cods.values().contains(tom));
```

An even more advanced filter mechanism is using the [Constraint](/javadoc/gems/org/picocontainer/gems/constraints/package-summary.html) extension as provided in the [picocontainer gems](http://www.picocontainer.org) .
