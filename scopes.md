---
layout: article
name: Scoped Containers
---

p. PicoContainer's scope hinges on parent/child container relationships. From a PicoContainer instance you can make a child container via makeChildContainer() or addChildContainer(..).

!/images/scopes.png(scopes illustration)!

p. These hierarchies of container can be set up with parents and children referring to each other, or children referring to parents only, or parents referring to children only. This changes the way that things work considerably. If a child has its parent registered, then it can use it for component resolution (but not without). If a parent has its child registered as a child, then it can manage it (start, stop, dispose cascade into children).

p. Web frameworks that leverage PicoContainer typically construct a tree of containers. For example an application level container maintains components that can be shared across the application. The application container will have child containers to maintain session level components. These session level containers can resolve dependencies through their parent containers, but the application container will have no visibility into its children. Additionally these child container do not have visibility into their sibling containers. With this design, containers can easily be garbage collected at the end of their scope.

h2. Simple Examples

p. Here are examples of parent/child setup:

{% highlight java %}
parent = new DefaultPicoContainer();  
parent.addComponent(Apple.class);  
parent.addComponent(Orange.class);  
parent.addComponent(Pear.class);  
child = parent.makeChildContainer();  
child.addComponent(DependsOnApple.class);  
// this is OK  
parent.start();  
// cascades to child just fine  

parent = new DefaultPicoContainer();  
parent.addComponent(Apple.class);  
parent.addComponent(Orange.class);  
parent.addComponent(Pear.class);  
child = parent.addChildContainer(new DefaultPicoContainer());  
child.addComponent(DependsOnApple.class);  
// this is NOT OK. child can't see parent  
parent.start();  
// cascades to child just fine  

parent = new DefaultPicoContainer();  
parent.addComponent(Apple.class).addComponent(Orange.class).addComponent(Pear.class);  
child = parent.addChildContainer(new DefaultPicoContainer(parent));  
child.addComponent(DependsOnApple.class);  
// this is OK  
parent.start();  
// cascades to child just fine  

parent = new DefaultPicoContainer();  
parent.addComponent(Apple.class).addComponent(Orange.class).addComponent(Pear.class);  
child = new DefaultPicoContainer(parent);  
child.addComponent(DependsOnApple.class);  
// this is  OK  
parent.start();  
// does NOT cascades to child
{% endhighlight %}

p. There is no limit to the extent of the chained containers, for example - great grandparent, grandparent, parent, child, is OK.

p. Garbage collection is the standard way of disposing of scoped containers. If the children refer to the parents, then assign child to null just works. If the parents refer to the children too, then you'll have to do a removeChildContainer(..) first before it will be eligible for garbage collection.

 *See "web frameworks":/web/*  *also.* 