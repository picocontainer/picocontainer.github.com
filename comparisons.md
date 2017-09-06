---
layout: article
name: Container Comparisons
---

h2. Overview

p. This document aims to compare PicoContainer to other IoC containers, both lightweight and not.

p. There are a number of published API specifications since Java was initially released that proport to be container/component designs. We discuss then here, suggesting there are goals that some have met, and some not. This may help you make better design choices for your own components.

h2. IoC Containers

h3. Spring Framework

p. The "Spring Framework":http://www.springframework.org/ is a J2EE framework. As such, "Dependency Injection":injection.html and "Lifecycle":lifecycle.html is only one of its concerns. PicoContainer, on the other hand, is concerned only with "Dependency Injection":injection.html , configuration and "Lifecycle":lifecycle.html .

h3. Apache Avalon and its containers

p. (Avalon ceased development in 2005)

p. Apache hosted a project that had been running for many years called Avalon. It had many components that fit that its design and many containers is written in Java. Avalon components were characterized by implementation of many optional interfaces. Avalon components were distributed with meta-information in XML in the jar file. More XML is required to assemble components together for the same of a application. Avalon Phoenix, Excalibur Component Manager (ECM), Avalon Fortress and Avalon Merlin were the pertinent containers.

p. The Avalon Framework required implementing components to implement a number of interfaces. By the end of the the project, this had proven historically to be a bit of a turn-off for component writers. Those interfaces were :-

Avalon InterfacePicoContainer equivalent|
|LogEnabled||Logging agnostic||
|Contextualizable||n/a||
|Serviceable (was Composable)||arguments are injected||
|Configurable||arguments are injected||
|Parameterizable||n/a||
|Initializable||Constructor is the equivalent lifecycle concept||
|Startable||Startable||
|Suspendable||n/a||
|Recontextualizable||n/a||
|Recomposable||n/a||
|Reconfigurable||n/a||
|Reparameterizable||n/a||
|Disposable||Disposable||

p. Avalon is a Contextualized Lookup IoC design.

h2. Example

{% highlight java %}
import org.apache.avalon.framework.ServiceManager;
import org.apache.avalon.framework.Serviceable;
import org.apache.avalon.framework.ServiceException;
public class Shop implements Serviceable, Initializable { 
  StockManager stockManager; 
  String shopZipCode;
  public void service(ServiceManager sm) throws ServiceException {
    stockManager = (StockManager) sm.lookup("StockManager");
  }
  public void initialize() { 
    // all service()ing has been done. 
  } 
}
{% endhighlight %}

p. A component has to have service (component) declarations in an external file. The "Loom":http://loom.codehaus.org/ container (forked from "Phoenix":http://www.axint.net/apache/avalon/phoenix/v4.0.2/ ) has .xinfo files for each component to meet such needs. All Avalon container have some mechanism for storing configuration and assembly externally to the class. Cross referenced against the xinfo files, Loom's assembly.xml defines the implementations to be used for component types. Thus all Avalon components must be interface/implementation separated. Another Avalon using server technology is "Keel":http://www.keelframework.org/ .

p. It has to be said that all post-Avalon projects are not as active as they could be.

h3. Using _Contextualized Dependency Lookup_ Components Without a Container.

p. The downside of the this design is that components can only be used without the container with great difficulty. If at all. Thus a proper container is needed at all times, and you have to choose one for different purposes. If you do manage to instantiate components without a container, you might miss one of the essential service dependencies. The component-using class will continue to compile, but at run time it will be apparent that there are missing dependencies. Because of the these complexities, unit testing with frameworks like "JUnit":http://www.junit.org/index.htm is very difficult for of Avalon components.

h2. Sun specified nearly-IoC Containers&Component designs

p. Sun have specified several container/component designs over the years.

h3. Enterprise Java Beans

p. Clearly Entity and Session beans run inside a container. The API is well defined, and to varying degrees of success one can deploy EJB applications to WebLogic, WebSphere, Orion and JBoss etc. For assembly and configuration, there is high use of element-normal XML . There are some mandated parent objects and interfaces for various to extend and/or implement. Resolution is done by the components themselves via JNDI more often than not.

p. PicoComponents are simpler in they they do not force an extensive XML markup, nor require the implementing of certain interfaces or extending base classes. Quite importantly the relationship between factory (home), implementation (bean) and interface (remote) parts is much more real in Pico-style components. EJB 2.0 components are nearly impossible to unit-test without much effort.

p. With the advent of EJB 3.0 (which the our team directly influenced), things became easier for EJB developers. Annotations were used to mark fields, and methods for injection. Strangely constructors were not eligible for injection. It was reported back to the our team that it was felt that the J2EE container makers would not find it easy making multi-argument constructor injection a reality.

h3. Servlets

p. Not so obvious - Servlets are contained by a servlet container. They are generally bundled with (or replaced by) value added propositions like JSP, but it is still a container/component design. High use of XML for assembly and configuration. Servlets have no concept of parent container or the container above that (sometimes EJB) and its provision of components, which is very unfortunate. Servlets have a number of interfaces to honor, none of which is too malignant. Servlets typically deal with external (or parent) components via RMI or JNDI. In more recent releases of EJB, local interfaces rather than RMI may be the mechanism for connection the parent components. WebLogic have always provided an optimizing mechanism for this interoperation

p. As with EJB, PicoComponents are far simpler. This is probably because they offer no web experience, without an extension. Servlets again are not that unit-testable.

h3. Applets

p. Applets, though presently not so often used, are a good example of Container/Component separations. There is very little XML in use by Applets. Configuration is typically delivered in applet tags in HTML. Applets are granted some access to the parent container, the browser, and its DOM model for pages and other applets. There very little standardization for Browser as a container.

p. As with EJB, PicoComponents are far simpler. Applets are unit-testable but with a little effort. Complex DOM interoperation is impossible under unit testing.

h3. Mainable (Not actually a container)

{% highlight java %}
public static void main(String[] args) {}
{% endhighlight %}

p. Familiar? Hopefully not !/images/smile.gif! Static plays no part in a good IoC container/component design. This includes static launching of Java Webstart (JNLP) applications. If you have to keep mainable functionality separate your components away from the main() class so they may be instantiated separately. In .NET you'll have to make sure that the application assembly is a small bootstrap to a component one.

h3. JNDI (Java API)

p. A huge map of clunky access components via a very non-IoC mechanism. It has to be strapped with much XML to prevent inappropriate access. This is not IoC because the component reaches out for external component dependancies whenever it feels like. This last fact clouds Serlvets and EJB use.

h3. AWT, Swing, SWT (Java graphic toolkits)

p. Nice container/component designs. In the case of Swing, perhaps a little difficult for coders to easily assemble applications.

h3. Eclipse (Java graphical application platform)

p. The "Eclipse":http://eclipse.org platform is very compelling. It supports the notion of a pluggable application concept. Each component statically accesses other components via a factory (which at least Paul does not like), though it is clear that some complex classloader magic is going on. The underpinning set of graphical components, SWT , are a simple and elegant design.

