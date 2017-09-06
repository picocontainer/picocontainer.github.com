------------------------------------------------------------------------

layout: article
name: Caching and Singletons
---

This is generally where a component has a single instance in the container that is returned to the called of getComponent(..) rather than a new instance being created each time the container is asked. Note that this is very different to the Singleton as defined by the Gang-of-Four book 'Design Patterns' where a class has a static getInstance() method and a private static instance variable.

### Basic Caching

Choose your style for caching behavior:

```java
pico = new DefaultPicoContainer(new Caching()); 
pico.addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // both the same instance

pico = new DefaultPicoContainer(); 
pico.as(CACHE).addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // both the same instance

pico = new PicoBuilder().withCaching().build(); 
pico.addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // both the same instance

import static org.picocontainer.behaviors.Behaviors.caching; 
... 
pico = new PicoBuilder().withBehaviors(caching()).build(); 
pico.addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // both the same instance
```

This is caching as above, but only when that property is set.

```java
pico = new DefaultPicoContainer(new OptinCaching()); 
pico.addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // different instances
```

```java
pico = new DefaultPicoContainer(new OptinCaching()); 
pico.as(CACHE).addComponent(Apple.class); 
Apple a1 = pico.getComponent(Apple.class); 
Apple a2 = pico.getComponent(Apple.class); // both the same instance
```

### Thread Caching

This is where a component has a single instance in the container rather that a new one created each time the container is asked for that type. The difference to 'Caching' is that there is one cache per thread using the container. For the most part you are not going to use this behavior unless you are 100% sure that the multi-threaded application you are writing maps threads to the notional session you are trying to model.

Choose your style for thread caching behavior:

```java
pico = new DefaultPicoContainer(new ThreadCaching()); 
pico.addComponent(Apple.class); 
new Thread() { 
	public void run() { 
		Apple a1 = pico.getComponent(Apple.class); 
		Apple a2 = pico.getComponent(Apple.class); 
	} 
}).start(); 
new Thread() { 
	public void run() { 
		Apple a3 = pico.getComponent(Apple.class); 
	} 
}).start(); 
// a1 and a2 are the same instance 
// a1 and a3 are different instances 
```

```java
pico = new DefaultPicoContainer(); 
pico.as(THREAD_CACHE).addComponent(Apple.class);
```

```java
pico = new PicoBuilder().withThreadCaching().build(); 
pico.addComponent(Apple.class);
```

### Storing

This is where a component has a single instance in the container rather that a new one created each time the container is asked for that type. This is like 'ThreadCaching' as it has the one cache per thread behavior, but the whole store is can be extracted and set outside if you have a reference to the Storing instance. Again you are likely to use this behavior if you are trying to model 'session' behavior. With this one though you able to retrieve the cache from some place associated with the session and set it for the thread before use. After use, it can be extracted and stored back with the session.

Only one style is shown here as you will need to hang on to the reference to the BehaviorFactory to make use of it:

```java
pico = new DefaultPicoContainer(new Storing()); 
pico.addComponent(Apple.class); 
new Thread() { 
	public void run() { 
		Apple a1 = pico.getComponent(Apple.class); 
		Apple a2 = pico.getComponent(Apple.class); 
	} 
}).start(); 
new Thread() { 
	public void run() { 
		Apple a3 = pico.getComponent(Apple.class); 
	} 
}).start(); 
// a1 and a2 are the same instance 
// a1 and a3 are different instances 
```

The BehaviorFactory for Storing behavior is not stateless. It allows the cache to be set for the thread as well as extracted. Here's a code snippet to illustrate that:

```java
storing = new Storing();  
pico = new DefaultPicoContainer(storing);  
....  
storing.putCacheForThread(httpRequest.getSession().getAttribute("sessionComponentStore");  
// do other methods that process request in session concept and may do getComponent(..) from the session container.  
httpRequest.getSession().setAttribute("sessionComponentStore", storing.getCacheForThread());
```

See [scopes](scopes.html) for another example for a web framework

### Historical Naming thoughts

The Spring Framework has traditionally had a 'Singleton Scope' for an object which is analogous. More recently, [Guice](http://code.google.com/p/google-guice/) explicitly called these Singletons. After Guice came out, and given the similarities to PicoContainer, we debated this, and decided that we should not call explicitly this a Singleton. As stated before, another PicoContainer instance could be managing another single instance of the same component, so it does not fit the definition of Singleton given in Design Patterns book. It is our recommendation that teams do not call components cached in a container singletons at all. Instead we suggest you refer to them by the scope you intend. Most likely this will be 'Application Scope', 'Session scope' and 'Request Scope' for web applications, and implicitly cached at that scope for its duration.
