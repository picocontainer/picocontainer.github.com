---
layout: article
name: Propagating Dependency
---

h2. Symptoms

p. A dependency exists solely to be propagated to another class, but no methods are called upon the dependent object itself. For example:

{% highlight java %}
public class AControllerImpl implements AController {
  private SomeService service;
  private int someId;
  public AControllerImpl(SomeService service) {
    this.service = service; this.someId = generateRandomNumber();
  }
  public launchAnotherController() { 
    AnotherController anotherController = new AnotherControllerImpl(service, fooId); 
    anotherController.launch(); 
  } 
  // ... 
}
{% endhighlight %}

p. In this example, no method-calls are made upon 'service', it is simply propagated to the constructor of 'AnotherControllerImpl'. Therefore, it is not a valid dependency for 'AControllerImpl'.

h2. Causes

p. DependencyInjection has been partially applied to a hierarchy of classes. This could be because some classes in the hierarchy depend upon instance state not available at container-registration time.

h2. What To Do

p. Apply DependencyInjection to 'AControllerImpl' by replacing the dependency on 'SomeService' with a dependency on 'AnotherController'. If, as in the example above, 'AnotherControllerImpl' has a dependency upon some state that is not available at container-registration time, then we need to introduce a factory for creating 'AnotherController' as follows:

p. TODO: This is maybe a little contrived for this example. Maybe remove or simplify. (AH).

{% highlight java %}
public class AControllerImpl implements AController {
  private AnotherControllerFactory anotherControllerFactory;
  private int someId;
  public AControllerImpl(AnotherControllerFactory anotherControllerFactory) {
    this.anotherControllerFactory = anotherControllerFactory; 
    this.someId = generateRandomNumber();
  }
  public launchAnotherController() { 
    AnotherController anotherController = anotherControllerFactory.createAnotherController(someId); 
    anotherController.launch(); 
  } 
  // ... 
}
{% endhighlight %}

p. 'AnotherControllerFactory' is an interface:

{% highlight java %}
public interface AnotherControllerFactory { 
  AnotherController createAnotherController(int someId); 
}
{% endhighlight %}

p. It can be implemented as follows:

{% highlight java %}
public class AnotherControllerFactoryImpl implements AnotherControllerFactory {
  private SomeService service;
  public AnotherControllerFactoryImpl(SomeService service) {
    this.service = service;
  }
  public AnotherController createAnotherController(int someId) { 
    return new AnotherControllerImpl(service, someId); 
  } 
}
{% endhighlight %}

p. Now we can register both 'AControllerImpl' and 'AnotherControllerFactoryImpl' in the container. When 'AControllerImpl' is instantiated, it is supplied with an implementation of 'AnotherControlFactory' that it can use to create an 'AnotherController' instance.

h1. Exceptions

p. When [Migrating from executors to services], it can sometimes be difficult to avoid introducing a Propagating Dependency. In these cases, the Propogating Dependency can be considered as a good first step towards PicoFication of a set of classes. An effort should be made to complete PicoFication at some stage by making a series of further steps as described above.

