---
layout: article
name: Disambiguation
---

## What is Disambiguation?

During your first foray into PicoContainer land, you might encounter an _AmbiguousComponentResolutionException_ like this:

```
org.picocontainer.injectors ".AbstractInjector$AmbiguousComponentResolutionExceptio":/javadoc/core/org/picocontainer/injectors/AbstractInjector.AmbiguousComponentResolutionException.html n: class<Something Here>needs a '<Something Here>' injected, but there are too many choices to inject. These:[class<Something Here>, class<Something Here>], refer http://picocontainer.org/help/ambiguous-injectable-help.html at org.picocontainer.parameters.BasicComponentParameter.getTargetAdapter(BasicComponentParameter.java:270) at org.picocontainer.parameters.BasicComponentParameter.resolveAdapter(BasicComponentParameter.java:205) at org.picocontainer.parameters.BasicComponentParameter.isResolvable(BasicComponentParameter.java:136) at org.picocontainer.parameters.ComponentParameter.isResolvable(ComponentParameter.java:132) at org.picocontainer.injectors.ConstructorInjector.getGreediestSatisfiableConstructor(ConstructorInjector.java:105) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:155) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.injectors.AbstractInjector.getComponentInstance(AbstractInjector.java:103) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:538)
```
	
Or possibly a _CyclicDependencyException_ like this:

```
org.picocontainer.injectors. "AbstractInjector$CyclicDependencyException":/javadoc/core/org/picocontainer/injectors/AbstractInjector.CyclicDependencyException.html : Cyclic dependency: [class<Something Here>, class<Something Here>, class<Something Here>] at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:257) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:516) at org.picocontainer.parameters.BasicComponentParameter.resolveInstance(BasicComponentParameter.java:146) at org.picocontainer.parameters.ComponentParameter.resolveInstance(ComponentParameter.java:119) at org.picocontainer.injectors.SingleMemberInjector.getMemberArguments(SingleMemberInjector.java:89) at org.picocontainer.injectors.ConstructorInjector.getMemberArguments(ConstructorInjector.java:199) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:162) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:516) at org.picocontainer.parameters.BasicComponentParameter.resolveInstance(BasicComponentParameter.java:146) at org.picocontainer.parameters.ComponentParameter.resolveInstance(ComponentParameter.java:119) at org.picocontainer.injectors.SingleMemberInjector.getMemberArguments(SingleMemberInjector.java:89) at org.picocontainer.injectors.ConstructorInjector.getMemberArguments(ConstructorInjector.java:199) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:162) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:538)
```	

And you say to your self something like," *What the heck happened?!?!?!?* "

The situation here is not as scary as it seems. We'll examine what causes the errors one at a time.

## CyclicDependencyException

Here's a test case that shows what can cause a CyclicDependencyException:

```
@Test 
public void testCircular() { 
  MutablePicoContainer mpc = new PicoBuilder().build(); 
  mpc.addComponent(List.class, ArrayList.class); 
  mpc.addComponent(Set.class, HashSet.class); 
  List l = mpc.getComponent(List.class); //Throws a CircularDependencyException 
}
```

Upon first glance at the code, you might think "Yeah, so?". But the problem here is that PicoContainer tries to satisfy the *greediest* constructor; or in other words, it looks for the constructor with the most arguments first. In this case, there is a constructor *ArrayList(Collection src),* and since it has the most arguments, PicoContainer uses this one.

Unfortunately, this results in the following situation:

1.  PicoContainer tries to instantiate *ArrayList* . It finds a constructor that requires a Collection.
2.  It searches itself and finds that it has an object that satisfies the Collection argument: the *HashSet* .
3.  It tries to instantiate the HashSet, and finds it has a constructor that requires are Collection.
4.  So it searches itself and finds an object that satisfies the Collection argument: the ArrayList.

And so on until we either run out of stack space or this document increases to 2 Gb in size explaining the recursion. Fortunately, PicoContainer is smart enough to know where there is a recursive satisfaction like this and throws an exception rather than going into infinite recursion.

That is what causes the Cyclic Dependency Injection.

So how do you solve it? Well, lets back up see what causes the AmbiguousComponentResolutionException first and *then* we'll talk about how to fix it. (We promise! We won't let you down!)

## AmbiguousComponentResolutionException

Here's a test case that shows what can cause AmbiguousComponentResolutionException:

```java
@Test public void testAmbiguous() { 
  MutablePicoContainer mpc = new PicoBuilder().build(); 
  mpc.addComponent(List.class, ArrayList.class); 
  mpc.addComponent(Set.class, HashSet.class); 
  mpc.addComponent(Collection.class, TreeSet.class); 
  List l = mpc.getComponent(List.class); //Throws a AmbiguousComponentResolutionException 
}
```

The only difference between this example and the CyclicDependencyException example is the introduction of a new object that can satisfy the *java.util.Collection* requirement.

## Review

Pico will automatically grab the argument that has the most arguments that can be satisfied.

So what happens here?

1. PicoContainer tries to instantiate *ArrayList* .
2. It finds that there are now **two** components that can satisfy the *ArrayList(Collection)* argument: *Set* , and *Collection* .

Rather than try to guess which one the developer intended to use. (And frankly, knowing us developers, Pico would guess wrong), we throw an AmbiguousComponentResolutionException instead.

So, what we really need is a way to specify that PicoContainer uses the exact constructor we want with the exact arguments. Making sure that PicoContainer understands which constructor to use in a situation like this is called"Disambiguation". PicoContainer provides several methods to disambiguate registrations.

Disambiguation using the [Parameter](/javadoc/core/org/picocontainer/Parameter.html) Objects.
---------------------------------------------------------------------------------------------

Careful examination of MutablePicoContainer.addComponent() will show an optional last object that takes a variable number of org.picocontainer.Parameter objects. Without going into the specifics yet, the general rule is that if you specify three parameter objects in your addComponent() registration, then you will use the constructor that takes three parameters in your object. Use Parameter.ZERO to force the default constructor.

There are two main types of Parameter objects: (Others are possible... hey, this *is* an interface we're talking about here.)

1. [Constant Parameter](/javadoc/core/org/picocontainer/parameters/ConstantParameter.html) : Used to specify constant values such as Integers, Strings, and so on into the component.
2. [Component Parameter](/javadoc/core/org/picocontainer/parameters/ComponentParameter.html) : Used to specify a reference to another object registered with addComponent() in the container.

With these options, it is time to revisit the AmbiguousComponentResolutionException example and modify it with our new found knowledge:

```java
@Test 
public void testAmbiguous() { 
  MutablePicoContainer mpc = new PicoBuilder().withCaching().build(); 
  mpc.addComponent(List.class, ArrayList.class, Parameter.ZERO); //Force Default Constructor 
  mpc.addComponent(Set.class, HashSet.class, new ConstantParameter(33)); //Equiv to new HashSet(33)
  mpc.addComponent(Collection.class, TreeSet.class, new ComponentParameter(List.class)); //Equiv to new TreeSet(mpc.getComponent(List.class)).
  Collection c = mpc.getComponent(TreeSet.class); 
}
```

So how do the Parameters modify the construction?

-  The Parameter.ZERO line forces ArrayList to be constructed with the default constructor. The result of mpc.getComponent(List.class) is new ArrayList()
-  The line with new ConstantParameter(33), forces HashSet to be instantiated with a one-argument constructor that uses an integer value of 33. In the case of HashSet, that would be the"initial capacity"argument, and thus mpc.getComponent(Set.class) results in new HashSet(33).
-  Finally, the ComponentParameter argument. By having the code in the example, we're telling Pico to use the 1-arg constructor in TreeSet that can be satisfied with the return value from mpc.getComponent(List.class). In this case, new TreeSet(Collection) satisfies the constructor arguments so after resolving the mpc.getComponent() instances, we have something similar to new TreeSet(new ArrayList);

## Note:

In this example, we turned on Caching. Because of this, the collection instances are shared. So the following code would work:

```java
List myList = mpc.getComponent(List.class); 
myList.add("This is a test"); 
Collection c = mpc.getComponent(Collection.class); 
assertEquals("This is a test", c.iterator().next());
```

Because of caching, the results of myList are shared with the TreeSet retrieved by mpc.getComponent(Collection). Also note, however that because the constructor HashSet(Collection) makes a copy of the passed in collection, the following code would NOT work:

```java
List myList = mpc.getComponent(List.class); 
myList.add("This is a test"); 
Collection c = mpc.getComponent(Collection.class); 
myList.add(0,"Another test"); 
assertEquals("This is a test", c.iterator().next()); //FAILS. The result of the evaluation is"Another Test"
```

Disambiguation using parameter names <span style="text-decoration: underline;;"></span>
---------------------------------------------------------------------------------------

With PicoContainer 2.0 we are able to leverage the parameter names of constructors and methods in order to remove the ambiguity on dependencies.

```java
public class Store { 
  public Store(StockManager workingDayStockManager, StockManager afterHoursStockManager) { 
    // etc 
  } 
}
```

If Store is added to the container appropriately, then its parameter names <span style="font-family: monospace;;">'</span> workingHoursStockManager' and 'afterHoursStockManager ' can be used in conjunction with similarly named components inject the right dependency in the right way:

```java
pico.as(Characteristics.USE_NAMES).addComponent(Store.class);
```

Access to parameter names was dropped from JDK 6.0 and it is uncertain whether it will be added as a feature in another release, so PicoContainer relies on another open source library called [Paranamer](http://paranamer.codehaus.org) , without requiring a dependency on its Jar. In other words, PicoContainer has the same classes from Paranamer in its jar.

See [CommandLineArgumentsPicoContainer](CommandLinePicoContainer.html) , [PropertiesPicoContainer](PropertiesPicoContainer.html) and [SystemPropertiesPicoContainer](SystemPropertiesPicoContainer.html) for implementations of PicoContainer that set up components for subsequent parameter name binding.

Page [component-configuration](component-configuration.html) also shows parameter names being used for binding as does [injection.](injection.html)

Usage of parameter names is available for all types of Dependency-Injection where parameter names are present.
<span style="text-decoration: underline;;"></span> h2. Disambiguation using Binding Annotations

This works the same as Guice. Namely you make an annotation that extends our 'Bind' annotation and mark it in your constructor or method's signature like so.

```java
@Retention(RetentionPolicy.RUNTIME) 
@Target({ElementType.FIELD, ElementType.PARAMETER}) 
@Bind 
public static @interface WorkingDayStockManager { } 
@Retention(RetentionPolicy.RUNTIME) 
@Target({ElementType.FIELD, ElementType.PARAMETER}) 
@Bind 
public static @interface AfterHoursStockManager { } 
public class Store { 
  public Store(@WorkingDayStockManager StockManager workingDayStockManager, 
               @AfterHoursStockManager StockManager afterHoursStockManager) { 
    // etc 
  } 
}
```

Making PicoContainer leverage binding annotations if present is automatic.

Binding annotations can be specified for constructor parameters (as shown above), method injection parameters as well as field injection.

### Where Next?

<span class="callout">The [Scoped Containers](scopes.html) page describes how trees of containers are good representations of scopes</span>
