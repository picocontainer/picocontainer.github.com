---
layout: article
name: DefaultPicoContainer
---

p. DefaultPicoContainer is the main workhorse container.

p. Here is an example of use:

{% highlight java %}
DefaultPicoContainer dpc = new DefaultPicoContainer(); 
dpc.addComponent(Foo.class, FooImpl.class); 
Foo foo = dpc.getComponent(Foo.class)
{% endhighlight %}

p. Parameters in its constructors can be:

*  "Behavior Factories or Component Factories":behaviors.html 
*  "Lifecycle Strategies":lifecycle.html 
*  "Component Monitors":monitors.html 
* Parent Containers of various types

