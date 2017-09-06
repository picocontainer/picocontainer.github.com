---
layout: article
name: FAQs
---

h3. Why PicoContainer 2.0?

p. First EJB 3.0 then Google's Guice seemed to copy the ideas from PicoContainer. EJB 3.0 invented the annotated types of injection, but crazily omitted the Constructor Injection support, and Guice too the annotation idea and went far to further with it. Soon after Guice, even the Spring Framework team decided to do things the direct way with their 'JavaConfig' capability.

p. So PicoContainer 1.x was refactored (not rewritten) into PicoContainer 2.0. All the unit tests were kept, and exercise took hundreds of commits, but the team is pleased with the result. PicoContainer has moved into the JDK 5 era and introduced some more innovations.

h3. Can my component use multiple constructors?

p. Yes.

p. You can code multiple constructors for a component:

{% highlight java %}
class MyComp {
  private ThreadPool theThreadPool;
  public MyComp(ThreadPool threadpool) {
    theThreadPool = threadpool;
  }
  public MyComp() {
    theThreadPool = new DefaultThreadPool(); 
  } 
  // other methods. 
}
{% endhighlight %}

h3. How does PicoContainer decide what constructor to use?

p. PicoContainer will instantiate a given component using the"greediest"satisfiable constructor. By greedy, we mean the constructor that takes the most parameters. By satisfiable, we mean constructors where all arguments can be satisfied by other registered components.

p. If you register a component with no satisfiable constructors, or two or more ambiguous"largest"constructors, a RuntimeException will be thrown when you ask for the component instance.

p. We recommend for most components, you use only one constructor, although this is by no means a requirement.

h3. How does PicoContainer compare to EJB containers?

p. PicoContainer is rather different. EJB traditionally has loads of things you must extend, implement, provide and throw. EJB itself is also not an Inversion of Control (IoC) design. It is close in that components are managed by a container, but the cumbersome and static nature of the JNDI lookups ensure that it is not actually IoC. PicoContainer is not a superset of EJB though, as it provides no remoting capability out of the box.

h3. How does PicoContainer decide what constructor to use?

p. PicoContainer will instantiate a given component using the"greediest"satisfiable constructor. By greedy, we mean the constructor that takes the most parameters. By satisfiable, we mean constructors where all arguments can be satisfied by other registered components.

p. If you register a component with no satisfiable constructors, or two or more ambiguous"largest"constructors, a RuntimeException will be thrown when you ask for the component instance.

p. We recommend, for the sake of predictability, that PicoContainer compatible components use only one constructor, although this is by no means a requirement.

h3. How to use primitive types in constructors?

p. PicoContainer will look for the greediest constructor of your component. But if your component's constructor depends on primitive types you may set the values explicitly.

{% highlight java %}
public interface ThreadPool { 
  void setSize(int);
}
public class MyComp {
  private ThreadPool threadPool;
  public MyComp(ThreadPool pool, int size) {
    threadPool = pool; 
    threadPool.setSize(size); 
  } 
}
{% endhighlight %}

p. In this case you can set the parameters at registration time:

{% highlight java %}
DefaultPicoContainer pico = new DefaultPicoContainer();
pico.addComponent(ThreadPool.class, DefaultThreadPool.class);
pico.addComponent(MyComp.class, MyComp.class, new Parameters[] { 
    new ComponentParameter(), 
    new ConstantParameter(new Integer(5)); 
}) 
MyComp myComp = pico.getInstance(MyComp.class);
{% endhighlight %}

p. Use ConstantParameter to set constant values and the ComponentParameter to let Pico resolve the dependency.

h3. When should I use PicoContainer?

p. We recommend using PicoContainer when your application consists of many different components that are related to each other. Instantiating a lot of objects and lacing/connecting them in the right order can be difficult task.

h3. Why Another IoC Framework?

p. After a long period of watching or participating in the development of Apache's Avalon framework, we formed a team to start what we believed to be a far simpler IoC design. One that considered the component contract more important that the container.

p. When we set out to do this in 2003, there was nothing equivalent to PicoContainer (xContainer originally). XWork, that underpinned WebWork2 was a general/embeddable Setter Dependency Injection framework, but not well known. The Spring Framework started in the same timescale, and we were talking to its authors before Martin Fowlers Dependency Injection article. Later we discovered that Apache's HiveMind also was in the same space.

h3. Why Constructor Injection?

p.  "Constructor Injection":constructor-injection.html is hard to swallow for developers who have been living with Interface Lookup (the Avalon or OSGi way) or "Setter Injection":setter-injection.html for long. We think it's like TDD. Once you get the grasp of it, you don't go back. Here are some benefits of Constructor Injection:

* Constructor Injection makes a stronger dependency contract
* It is more succinct in terms of lines of code
* It is more succinct in terms dependency-statement-mechanism i.e. no XML, attributes, enabler interfaces etc
* A component that is Interface/Implementation separated should have the interface be the contract offered to other components, and the implementation potentially having some extra methods that are nothing to do with the contract.
* No indeterminate state. For setter injection and alike, not all the post instantiation setters may be called. Consider the non-container case for comp usage, if v1.2 of that comp introduces a new dependency, with "Constructor Injection":constructor-injection.html the compiler will tell you.


h3. Will we support Guice's @Inject annotation transparently?

p. We could compile PicoContainer against Guice.jar and leverage the @Inject method with a new InjectionFactory (and probably will). The problem is that there is no easy way to use an annotation without having its class in the classpath. There's no easy 'transparent' solution for this.

p. Things fail silently if the jar is missing, unless the annotation is used - then it fails noisily. Meaning, the mere declaration of an annotation on a component is not enough to cause a ClassNotFoundException on load, if its not used:

{% highlight java %}
@Retention(RetentionPolicy.RUNTIME) 
@Target(value={ElementType.METHOD}) 
public static @interface Bar { } 
... 
import com.google.inject.Inject;
public class Foo { 
  @Inject @Bar 
  public void setFoo(String str) { 
    System.out.println("setFoo:" + str);
  }
  public static void main(String[] args) throws NoSuchMethodException { 
    Foo foo = new Foo();
    foo.setFoo("hello"); 
    Method m = Foo.class.getMethod("setFoo", String.class);
    Annotation[] anns = m.getDeclaredAnnotations(); 
    System.out.println("Num annotations: " + anns.length);
    for (int i = 0; i<anns.length; i++) { 
	  Annotation ann = anns[i]; System.out.println("Annotation: " + ann.toString()); 
	} 
	Annotation ann = m.getAnnotation(Inject.class); 
	System.out.println("InjectAnnotation: " + ann); 
  } 
}
{% endhighlight %}

p. Leaving Guice out of the classpath, and trying to launch Foo like so:

{% highlight java %}
commandline: java -cp classes Foo  
setFoo:>hello  
Num annotations: 1  
Annotation: @Foo$Bar()  
Exception in thread "main" java.lang.NoClassDefFoundError: com/google/inject/Inject  
        at Foo.main(Foo.java:32)
{% endhighlight %}

p. The class loads, but the @Inject annotation is not present in the set of declared annotations, but another in the classpath is. Lastly when accessed explicitly, there is an exception thrown. Contrast to, the same run with Guice in the classpath:

{% highlight java %}
setFoo:hello  
Num annotations: 2  
Annotation: @org.picocontainer.annotations.Inject()  
Annotation: @Foo$Bar()  
InjectAnnotation: @org.picocontainer.annotations.Inject()
{% endhighlight %}