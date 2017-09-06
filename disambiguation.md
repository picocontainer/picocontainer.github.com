---
layout: article
name: Disambiguation
---

h2. What is Disambiguation?

During your first foray into PicoContainer land, you might encounter an _AmbiguousComponentResolutionException_ like this:
org.picocontainer.injectors ".AbstractInjector$AmbiguousComponentResolutionExceptio":/javadoc/core/org/picocontainer/injectors/AbstractInjector.AmbiguousComponentResolutionException.html n: class<Something Here>needs a '<Something Here>' injected, but there are too many choices to inject. These:[class<Something Here>, class<Something Here>], refer http://picocontainer.org/help/ambiguous-injectable-help.html at org.picocontainer.parameters.BasicComponentParameter.getTargetAdapter(BasicComponentParameter.java:270) at org.picocontainer.parameters.BasicComponentParameter.resolveAdapter(BasicComponentParameter.java:205) at org.picocontainer.parameters.BasicComponentParameter.isResolvable(BasicComponentParameter.java:136) at org.picocontainer.parameters.ComponentParameter.isResolvable(ComponentParameter.java:132) at org.picocontainer.injectors.ConstructorInjector.getGreediestSatisfiableConstructor(ConstructorInjector.java:105) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:155) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.injectors.AbstractInjector.getComponentInstance(AbstractInjector.java:103) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:538)
	
p. Or possibly a _CyclicDependencyException_ like this:

org.picocontainer.injectors. "AbstractInjector$CyclicDependencyException":/javadoc/core/org/picocontainer/injectors/AbstractInjector.CyclicDependencyException.html : Cyclic dependency: [class<Something Here>, class<Something Here>, class<Something Here>] at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:257) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:516) at org.picocontainer.parameters.BasicComponentParameter.resolveInstance(BasicComponentParameter.java:146) at org.picocontainer.parameters.ComponentParameter.resolveInstance(ComponentParameter.java:119) at org.picocontainer.injectors.SingleMemberInjector.getMemberArguments(SingleMemberInjector.java:89) at org.picocontainer.injectors.ConstructorInjector.getMemberArguments(ConstructorInjector.java:199) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:162) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:516) at org.picocontainer.parameters.BasicComponentParameter.resolveInstance(BasicComponentParameter.java:146) at org.picocontainer.parameters.ComponentParameter.resolveInstance(ComponentParameter.java:119) at org.picocontainer.injectors.SingleMemberInjector.getMemberArguments(SingleMemberInjector.java:89) at org.picocontainer.injectors.ConstructorInjector.getMemberArguments(ConstructorInjector.java:199) at org.picocontainer.injectors.ConstructorInjector$1.run(ConstructorInjector.java:162) at org.picocontainer.injectors.AbstractInjector$ThreadLocalCyclicDependencyGuard.observe(AbstractInjector.java:262) at org.picocontainer.injectors.ConstructorInjector.getComponentInstance(ConstructorInjector.java:192) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:65) at org.picocontainer.behaviors.Stored.getComponentInstance(Stored.java:85) at org.picocontainer.behaviors.AbstractBehavior.getComponentInstance(AbstractBehavior.java:61) at org.picocontainer.DefaultPicoContainer.getInstance(DefaultPicoContainer.java:559) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:526) at org.picocontainer.DefaultPicoContainer.getComponent(DefaultPicoContainer.java:538)
	
p. And you say to your self something like," *What the heck happened?!?!?!?* "

p. The situation here is not as scary as it seems. We'll examine what causes the errors one at a time.

h4. CyclicDependencyException

p. Here's a test case that shows what can cause a CyclicDependencyException:

{% highlight java %}
@Test 
public void testCircular() { 
  MutablePicoContainer mpc = new PicoBuilder().build(); 
  mpc.addComponent(List.class, ArrayList.class); 
  mpc.addComponent(Set.class, HashSet.class); 
  List l = mpc.getComponent(List.class); //Throws a CircularDependencyException 
}
{% endhighlight %}

p. Upon first glance at the code, you might think"Yeah, so?". But the problem here is that PicoContainer tries to satisfy the _greediest_ constructor; or in other words, it looks for the constructor with the most arguments first. In this case, there is a constructor _ArrayList(Collection src),_ and since it has the most arguments, PicoContainer uses this one.

p. Unfortunately, this results in the following situation:

# PicoContainer tries to instantiate _ArrayList_ . It finds a constructor that requires a Collection.
# It searches itself and finds that it has an object that satisfies the Collection argument: the _HashSet_ .
# It tries to instantiate the HashSet, and finds it has a constructor that requires are Collection.
# So it searches itself and finds an object that satisfies the Collection argument: the ArrayList.

p. And so on until we either run out of stack space or this document increases to 2 Gb in size explaining the recursion. Fortunately, PicoContainer is smart enough to know where there is a recursive satisfaction like this and throws an exception rather than going into infinite recursion.

p. That is what causes the Cyclic Dependency Injection.

p. So how do you solve it? Well, lets back up see what causes the AmbiguousComponentResolutionException first and _then_ we'll talk about how to fix it. (We promise! We won't let you down!)

h3. AmbiguousComponentResolutionException

p. Here's a test case that shows what can cause AmbiguousComponentResolutionException:

{% highlight java %}
@Test public void testAmbiguous() { 
  MutablePicoContainer mpc = new PicoBuilder().build(); 
  mpc.addComponent(List.class, ArrayList.class); 
  mpc.addComponent(Set.class, HashSet.class); 
  mpc.addComponent(Collection.class, TreeSet.class); 
  List l = mpc.getComponent(List.class); //Throws a AmbiguousComponentResolutionException 
}
{% endhighlight %}

p. The only difference between this example and the CyclicDependencyException example is the introduction of a new object that can satisfy the _java.util.Collection_ requirement.

h4. Review

p. Pico will automatically grab the argument that has the most arguments that can be satisfied.

p. So what happens here?

# PicoContainer tries to instantiate _ArrayList_ .
# It finds that there are now *two* components that can satisfy the _ArrayList(Collection)_ argument: _Set_ , and _Collection_ .

p. Rather than try to guess which one the developer intended to use. (And frankly, knowing us developers, Pico would guess wrong), we throw an AmbiguousComponentResolutionException instead.

p. So, what we really need is a way to specify that PicoContainer uses the exact constructor we want with the exact arguments. Making sure that PicoContainer understands which constructor to use in a situation like this is called"Disambiguation". PicoContainer provides several methods to disambiguate registrations.

h2. Disambiguation using the "Parameter":/javadoc/core/org/picocontainer/Parameter.html Objects.

p. Careful examination of MutablePicoContainer.addComponent() will show an optional last object that takes a variable number of org.picocontainer.Parameter objects. Without going into the specifics yet, the general rule is that if you specify three parameter objects in your addComponent() registration, then you will use the constructor that takes three parameters in your object. Use Parameter.ZERO to force the default constructor.

p. There are two main types of Parameter objects: (Others are possible... hey, this _is_ an interface we're talking about here.)

#  "Constant Parameter":/javadoc/core/org/picocontainer/parameters/ConstantParameter.html : Used to specify constant values such as Integers, Strings, and so on into the component.
#  "Component Parameter":/javadoc/core/org/picocontainer/parameters/ComponentParameter.html : Used to specify a reference to another object registered with addComponent() in the container.

p. With these options, it is time to revisit the AmbiguousComponentResolutionException example and modify it with our new found knowledge:

{% highlight java %}
@Test 
public void testAmbiguous() { 
  MutablePicoContainer mpc = new PicoBuilder().withCaching().build(); 
  mpc.addComponent(List.class, ArrayList.class, Parameter.ZERO); //Force Default Constructor 
  mpc.addComponent(Set.class, HashSet.class, new ConstantParameter(33)); //Equiv to new HashSet(33)
  mpc.addComponent(Collection.class, TreeSet.class, new ComponentParameter(List.class)); //Equiv to new TreeSet(mpc.getComponent(List.class)).
  Collection c = mpc.getComponent(TreeSet.class); 
}
{% endhighlight %}

p. So how do the Parameters modify the construction?

* The Parameter.ZERO line forces ArrayList to be constructed with the default constructor. The result of mpc.getComponent(List.class) is new ArrayList()
* The line with new ConstantParameter(33), forces HashSet to be instantiated with a one-argument constructor that uses an integer value of 33. In the case of HashSet, that would be the"initial capacity"argument, and thus mpc.getComponent(Set.class) results in new HashSet(33).
* Finally, the ComponentParameter argument. By having the code in the example, we're telling Pico to use the 1-arg constructor in TreeSet that can be satisfied with the return value from mpc.getComponent(List.class). In this case, new TreeSet(Collection) satisfies the constructor arguments so after resolving the mpc.getComponent() instances, we have something similar to new TreeSet(new ArrayList);

h4. Note:

p. In this example, we turned on Caching. Because of this, the collection instances are shared. So the following code would work:

{% highlight java %}
List myList = mpc.getComponent(List.class); 
myList.add("This is a test"); 
Collection c = mpc.getComponent(Collection.class); 
assertEquals("This is a test", c.iterator().next());
{% endhighlight %}

p. Because of caching, the results of myList are shared with the TreeSet retrieved by mpc.getComponent(Collection). Also note, however that because the constructor HashSet(Collection) makes a copy of the passed in collection, the following code would NOT work:

{% highlight java %}
List myList = mpc.getComponent(List.class); 
myList.add("This is a test"); 
Collection c = mpc.getComponent(Collection.class); 
myList.add(0,"Another test"); 
assertEquals("This is a test", c.iterator().next()); //FAILS. The result of the evaluation is"Another Test"
{% endhighlight %}

h2. Disambiguation using parameter names %{text-decoration: underline;}% 

p. With PicoContainer 2.0 we are able to leverage the parameter names of constructors and methods in order to remove the ambiguity on dependencies.

{% highlight java %}
public class Store { 
  public Store(StockManager workingDayStockManager, StockManager afterHoursStockManager) { 
    // etc 
  } 
}
{% endhighlight %}

If Store is added to the container appropriately, then its parameter names %{font-family: monospace;}'% workingHoursStockManager' and 'afterHoursStockManager ' can be used in conjunction with similarly named components inject the right dependency in the right way:

{% highlight java %}
pico.as(Characteristics.USE_NAMES).addComponent(Store.class);
{% endhighlight %}

p. Access to parameter names was dropped from JDK 6.0 and it is uncertain whether it will be added as a feature in another release, so PicoContainer relies on another open source library called "Paranamer":http://paranamer.codehaus.org , without requiring a dependency on its Jar. In other words, PicoContainer has the same classes from Paranamer in its jar.


See "CommandLineArgumentsPicoContainer":CommandLinePicoContainer.html , "PropertiesPicoContainer":PropertiesPicoContainer.html and "SystemPropertiesPicoContainer":SystemPropertiesPicoContainer.html for implementations of PicoContainer that set up components for subsequent parameter name binding.

Page "component-configuration":component-configuration.html also shows parameter names being used for binding as does "injection.":injection.html 

Usage of parameter names is available for all types of Dependency-Injection where parameter names are present.
 %{text-decoration: underline;}% h2. Disambiguation using Binding Annotations

p. This works the same as Guice. Namely you make an annotation that extends our 'Bind' annotation and mark it in your constructor or method's signature like so.

{% highlight java %}
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
{% endhighlight %}

p. Making PicoContainer leverage binding annotations if present is automatic.

p. Binding annotations can be specified for constructor parameters (as shown above), method injection parameters as well as field injection.

h3. Where Next?

 %(callout)The "Scoped Containers":scopes.html page describes how trees of containers are good representations of scopes% 