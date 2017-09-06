---
layout: article
name: TransientPicoContainer
---

p. TransientPicoContainer is a for short term needs for object instantiation, where the container is going to be de-referenced shortly after use.

p. Here is an example of use:

{% highlight java %}
public Foo makeSuitableFoo() { 
	MutablePicoContainer temp = new TransientPicoContainer(someParentContainer); 
	temp.addComponent(somethingElseThatMightBeNeeded); // by instance 
	temp.addComponent(Foo.class, FooImpleThatNeedsTheAbove.class); // by type return 
	temp.getComponent(Foo.class); 
}
{% endhighlight %}