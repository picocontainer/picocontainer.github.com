---
layout: article
name: SystemPropertiesPicoContainer
---

p. SystemPropertiesPicoContainer is a special type of PicoContainer that is populated from the "System Properties":http://www.mindspring.com/~mgrand/java-system-properties.htm passed into the Java application when it started.

p. Here is an example of how to use SystemPropertiesPicoContainer to map system properties as configuration for a component, with parameter names as the bindings.

p. Consider this component:

{% highlight java %}
public class BigFatComponent { 
  URL spellWebService; 
  int timeout;
  public BigFatComponent(String spellingWebServiceURL, int timeout) throws MalformedURLExeption {
    this.spellWebService = new URL(spellingWebServiceURL); 
    this.timeout = timeout;
  }
  public String toString() { 
    return "URL=" + spellWebService + ", timeout=" + timeout; 
  } 
  // other methods 
}
{% endhighlight %}

p. For automatic binding of system properties to parameter names of the component:

{% highlight java %}
PicoContainer syspropsContainer = new SystemPropertiesPicoContainer(); 
MutablePicoContainer mpc = new DefaultPicoContainer(syspropsContainer); 
mpc.as(Characteristics.USE_NAMES).addComponent(BigFatComponent.class);
{% endhighlight %}

p. Here is another manual use forcing an association between a system properties, and the relative position of a parameter in the constructor. It is most useful when there is not a tie-up between the parameter names and the system properties. In this case the arg passed in to the ConfigParameter (varargs list) is the one that matches the system property name:

{% highlight java %}
mpc.addComponent(BigFatComponent.class, ConfigParameter("wsURL"), ConfigParameter("serviceTimeout"));
{% endhighlight %}

p. System properties can be set for the scope of the application only, as you start the application:

{% highlight java %}
java -Dwebservice.url=http://servicehost/service -Dwebservice.timeout=50 -jar myAppJar.jar -cp picocontainer-2.0.jar
{% endhighlight %}

p. Type conversion is automatic in many cases. Properties in a properties file are clearly strings. Parameters can be any of:

* String
* All numeric types, boxed as well as primitives
* java.util.Date
* java.io.File as well as java.io.URL
* java.util.Date
* java.sql.(Date|Time|Timestamp)
* Any type sporting accessible constructor taking single string parameter.

p. SystemPropertiesPicoContainer can be part of a tree of containers. It makes sense, of course, being more near the root of that tree.

