---
layout: article
name: CommandLinePicoContainer
---

CommandLineArgumentsPicoContainer is a special type of PicoContainer that processes the arguments passed into from the command line, and sets them up as configuration for other components.

Here is an example of how to use CommandLineArgumentsPicoContainer to map command line arguments as configuration for a component, with parameter names as the bindings.

Consider this component:

```java
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
```

For automatic binding of command line arguments to parameter names of the component:

```java
public static void main(String[] args) { 
  PicoContainer argsContainer = new CommandLineArgumentsPicoContainer(args); 
  MutablePicoContainer mpc = new DefaultPicoContainer(argsContainer); 
  mpc.as(Characteristics.USE_NAMES).addComponent(BigFatComponent.class); 
  Object o = mpc.getComponent(BigFatComponent.class); 
} 
... 
java -jar myAppJar.jar -cp picocontainer-2.x.jar timeout=100 spellingWebServiceURL=http://ws.com/someService.wsdl
```

Here is another manual use forcing an association between a item in the properties file, and the relative 
position of a parameter in the constructor:

```java
mpc.addComponent(BigFatComponent.class, ConfigParameter("config1"), ConfigParameter("moreConfig"));
...
java -jar myAppJar.jar -cp picocontainer-2.x.jar config1=http://ws.com/someService.wsd moreConfig=100
```

Type conversion is automatic in many cases. Properties in a properties file are clearly strings. Parameters can be any of:

-   String
-   All numeric types, boxed as well as primitives
-   java.util.Date
-   java.io.File as well as java.io.URL
-   java.util.Date
-   java.sql.(Date|Time|Timestamp)
-   Any type sporting accessible constructor taking single string parameter.

CommandLineArgumentsPicoContainer can be part of a tree of containers. It makes sense, of course, being more 
near the root of that tree.
