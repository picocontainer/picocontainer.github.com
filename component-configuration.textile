---
layout: article
name: Component Configuration
---

h2. Choices

p. In true IoC style, configuration for a component is pushed into it.

p. With PicoContainer, there are a few ways to do this:

* Design a class to hold the configuration and have getters on it to allow depending components to access configuration.
* Manually set the configuration for components as you add them to a container
* Leverage the fact that PicoContainer can pick up on named components

h3. Configuration Object

{% highlight java %}
public interface BigFatComponentConfig { 
  String getSpellingWebServiceURL(); 
  String getTimeout();
}
public class BigFatComponent { 
  URL spellWebService; 
  String timeout;
  public BigFatComponent(BigFatComponentConfig config) {
    this.spellWebService = new URL(config.getSpellingWebServiceURL()); 
    this.timeout = config.getTimeout(); 
  } 
  // other methods 
}
...
pico.addComponent(BigFatComponent.class)
pico.addComponent(new BigFatComponentConfigBean("http://foo.com/foo.wsdl", 120))
{% endhighlight %}

h3. Setting config as part of addComponent(..)

{% highlight java %}
public class BigFatComponent { 
  URL spellWebService; 
  int timeout;
  public BigFatComponent(String spellingWebServiceURL, int timeout) {  
    this.spellWebService = new URL(spellingWebServiceURL); 
    this.timeout = timeout; 
  } 
  // other methods 
}
...
pico.addComponent(BigFatComponent.class, new ConstantParameter("http://foo.com/foo.wsdl"), new ConstantParameter(120));
{% endhighlight %}

h3. Configuration using parameter names

p. Consider this component ...

{% highlight java %}
public class BigFatComponent { 
  URL spellWebService; 
  int timeout;
  public BigFatComponent(String spellingWebServiceURL, int timeout) throws MalformedURLExeption {
    this.spellWebService = new URL(spellingWebServiceURL); 
    this.timeout = timeout;
  }
  public String toString() { 
    return"URL="+ spellWebService +", timeout="+ timeout; 
  } 
  // other methods }
{% endhighlight %}

p. And this composition ...

{% highlight java %}
pico.addConfig("spellingWebServiceURL","http://foo.com/spelling.wsdl"); // matches constructor parameter name pico.addConfig("somethingOtherURL","http://bar.com/other.wsdl");
pico.addConfig("timeout", 120); // matches constructor parameter name 
pico.addConfig("timeout2", 456);
pico.as(Characteristics.USE_NAMES).addComponent(BigFatComponent.class); 
BigFatComponent bfc = pico.getComponent(BigFatComponent.class); 
System.out.println("bfc:"+ bfc.toString()); // Prints .."bfg: URL=http://foo.com/spelling.wsdl, timeout=120"
{% endhighlight %}

p. This feature of PicoContainer relies on the a non-JDK mechanism of *accessing parameter names for methods and constructors* . In this case named parameters spellingWebServiceURL and timeout.

p. There are two ways for this to work:

* relying on the fact that your component classes have debug info compiled into them.
* post-processing component classes with "Paranamer":http://paranamer.codehaus.org/ before placing them in their jar

p. If the parameter name information cannot be accessed at runtime by PicoContainer, then Pico will be unable to differentiate between potential injectable classes of the same type and throws an exception that suggests that there is an ambiguity.

p. This is *not* a global variable facility. Its scoped with the container in question and its child containers.

h2. Taking configuration from the elsewhere

p. Configuration can also be bound to components with :

bq. p.  "CommandLineArgumentsPicoContainer":CommandLinePicoContainer.html 

p.  "PropertiesPicoContainer":PropertiesPicoContainer.html 

p.  "SystemPropertiesPicoContainer":SystemPropertiesPicoContainer.html 



