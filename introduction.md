---
layout: article
name: Introduction
---

p. Authors: Aslak Hellesoy, Jon Tirsen

h2. Basics

p. This is a quick introduction to PicoContainer's most important features. Read through it to get an idea of what PicoContainer is and isn't.

p. PicoContainer's most important feature is its ability to instantiate arbitrary objects. This is done through its API, which is similar to a hash table. You can put java.lang.Class objects in and get object instances back.

p. Example:

{% highlight java %}
MutablePicoContainer pico = new DefaultPicoContainer();
pico.addComponent(ArrayList.class); 
List list = pico.getComponent(ArrayList.class);
{% endhighlight %}

p. (i) "MutablePicoContainer API":/javadoc/core/org/picocontainer/MutablePicoContainer.html 

p. This code does the same as this:

{% highlight java %}
List list = new ArrayList();
{% endhighlight %}

p. With a trivial example such as this there is no point in using PicoContainer. This was just to illustrate the basic API. PicoContainer becomes useful with larger number of classes and interfaces having complex dependencies between each other:

|_. Complex Dependencies |_. Juicer Example |
| !images/complex-dependencies.png! | !images/juicer-peeler-peelable.png! |

p. (Green means class, Yellow means interface). PicoContainer identifies dependencies by looking at the constructors of registered classes ( "Constructor Injection":constructor-injection.html ). PicoContainer can also be though of as a generic factory that can be configured dynamically. PicoContainer is able to instantiate a complex graph of several interdependent objects.

h2. Write some simple classes and interfaces with dependencies

p. The "Juicer Example" diagram above could translate to the following code (we added a concrete Peelable):

p.  !images/apple-implements-peelable.png! 

{% highlight java %}
public interface Peelable { 
  void peel(); 
}
public class Apple implements Peelable { 
  public void peel() { } 
}
public class Peeler implements Startable {
  private final Peelable peelable;
  public Peeler(Peelable peelable) {
    this.peelable = peelable;
  }
  public void start() { 
	peelable.peel();
  }
  public void stop() { } 
}
public class Juicer {
  private final Peelable peelable;
  private final Peeler peeler;
  public Juicer(Peelable peelable, Peeler peeler) {
    this.peelable = peelable; 
    this.peeler = peeler; 
  } 
}
{% endhighlight %}

p. (Note that this code suffers from the anti-pattern "Propagating Dependency":antipatterns/propagating-dependency-antipattern.html but let's not worry about that for now !/images/smile.gif!)

h2. Assemble components

p. You tell PicoContainer what classes to manage by registering them like this (the order of registration has no significance):

{% highlight java %}
MutablePicoContainer pico = new DefaultPicoContainer();
pico.addComponent(Apple.class);
pico.addComponent(Juicer.class);
pico.addComponent(Peeler.class);
{% endhighlight %}

p. (i) "MutablePicoContainer API":javadoc/core/org/picocontainer/MutablePicoContainer.html 

h2. Instantiate components

p. You can tell PicoContainer to give you an instance of a class like this (provided it has been registered previously):

{% highlight java %}
Juicer juicer = (Juicer) pico.getComponent(Juicer.class);
{% endhighlight %}

p. This will cause PicoContainer to do something similar to this behind the scenes (except that PicoContainer uses reflection):

{% highlight java %}
Peelable peelable = new Apple(); 
Peeler peeler = new Peeler(peelable); 
Juicer juicer = new Juicer(peelable, peeler); 
return juicer;
{% endhighlight %}

p.  !/images/information.gif! Note how PicoContainer figures out that Apple is a Peelable, so that it can be passed to Peeler and Juicer's constructors.

h2. Container hierarchies

p. PicoContainer provides a powerful alternative to the "Singleton":antipatterns/singleton-antipattern.html . With container hierarchies you can create singleton-like objects where you have fine grained control over the visibility scope of the instance. (The singleton pattern is static and global - it won't allow more than one instance, and it is visible from anywhere. Not nice when you try to build a large enterprise application from it).

p.  !images/pico-hierarchy.png! 

p. A container (and its registered components) can get access to components registered in a parent container, but not vice-versa. Consider this example, using the classes from above:

p. !/images//warning.gif! THIS WON'T WORK! It is for illustration purposes only! !/images//warning.gif! 

{% highlight java %}
// Create x hierarchy of containers 
MutablePicoContainer x = new DefaultPicoContainer();
MutablePicoContainer y = new DefaultPicoContainer( x ); 
MutablePicoContainer z = new DefaultPicoContainer( y ); 
// Assemble components
x.addComponent(Apple.class); 
y.addComponent(Juicer.class); 
z.addComponent(Peeler.class); 
// Instantiate components 
Peeler peeler = (Peeler) z.getComponent(Peeler.class); // WON'T WORK! peeler will be null 
peeler = (Peeler) x.getComponent(Peeler.class); // WON'T WORK! This will throw an exception 
Juicer juicer = (Juicer) y.getComponent(Juicer.class);
{% endhighlight %}

p. This can be visualised as follows: !images/juicer-in-pico-hierarchy.png! 

p. Let's analyse what will happen here:

* Line 12 will work fine. z will be able to resolve the dependencies for Peeler (which is Fruit) from the parent container.
* Line 14 will return null, as x can't see Peeler.
* Line 16 will throw an exception, since Juicer's dependency to Peeler can't be satisfied (z can't be seen by y).

p. !/images//warning.gif! Since this obviously won't work, keep in mind that this was just an exercise to illustrate how container hierarchies work. !/images//information.gif! For a more concrete example of the usage of container hierarchies, see "PicoContainer Web":http://www.picocontainer.org/web .

h2. Lifecycle

p. PicoContainer has support for "Lifecycle":lifecycle.html . If your classes implement "Startable":/javadoc/core/org/picocontainer/Startable.html , you can control the lifecycle of all your objects with a simple method call on the container. The container will figure out the correct order of invocation of start()/stop() all the objects managed by the container.

p. Calling start() on the container will call start() on all container managed objects in the order of their instantiation. This means starting with the ones that have no dependencies, and ending with the ones that have dependencies on others:

|_. MutablePicoContainer.start() |_. MutablePicoContainer.stop()! |
| !images/start-container.png! | !images/stop-container.png! |

p. Lifecycle also works for hierarchies of containers. Calling start() on a container with child containers will start all the containers in a breadth-first order, starting with itself. Likewise, calling stop() will call stop() on all containers in the hierarchy in a depth-first order. The pictures below show what happens when start() and stop() are called on a container with children.

|_. MutablePicoContainer.start() |_. MutablePicoContainer.stop() |
| !images/start-hierarchy.png! | !images/stop-hierarchy.png! |

p.  !/images//warning.gif! In order for hierarchy-aware lifecycle to work, child containers must be registered as components in their parent container. Just creating a container with another one as a parent will *not* cause the parent container to know about the child container.

h3. Example

{% highlight java %}
MutablePicoContainer parent = new DefaultPicoContainer(new Caching()); 
MutablePicoContainer child = new DefaultPicoContainer(parent); // We must let the parent container know about the child container. 
parent.addComponent(child); // This will start the parent, which will start the child. 
parent.start();
{% endhighlight %}

p. !/images//warning.gif! Lifecycle is really only going to work for PicoContainers that are also caching component instances. Caching was a default in PicoContainer 1.x, but is not for 2.x - be warned!

p. !/images//warning.gif! Calling lifecycle methods on a container that has a parent container will *not* propagate the lifecycle to the parent container.

p. Read more about lifecycle "here":lifecycle.html .

h3. Contrasting Usage Styles

p. With PicoContainer you add components and get instances out in two styles.

p. Classic bean style:

{% highlight java %}
pico = new DefaultPicoContainer();
pico.addComponent(ComponentOneImpl.class) // by type 
pico.addComponent(ComponentTwoImpl.class) // by type 
pico.addComponent(new ComponentThreeImpl()) // by instance 
pico.addComponent(ComponentFourImpl.class) // by type 
ComponentFourImpl four = pico.getComponent(ComponentFourImpl.class);
{% endhighlight %}

p. Or you can use a "fluent":http://www.martinfowler.com/bliki/FluentInterface.html style if you want:

{% highlight java %}
ComponentFour four = new DefaultPicoContainer().addComponent(ComponentOne.class) 
    .addComponent(ComponentTwo.class).addComponent(new ComponentThree())
    .addComponent(ComponentFour.class).getComponent(ComponentFour.class);
{% endhighlight %}

h3. Where Next?

 %(callout)The "Dependency Injection":injection.html page discusses the types of Dependency Injection, with examples% 