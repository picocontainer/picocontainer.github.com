---
layout: article
title: PropertiesPicoContainer
---

PropertiesPicoContainer is a special type of PicoContainer that is populated from the entries in a properties file.

Here is an example of how to use PropertiesPicoContainer to map properties as configuration for a component, with parameter names as the bindings.

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

For automatic binding of properties to parameter names of the component:

```java
Properties defaultProperties = new Properties(); 
defaultProperties.load("my-application.properties"); // default properties are taken from here 
PicoContainer propsContainer = new PropertiesPicoContainer(defaultProperties); 
MutablePicoContainer mpc = new DefaultPicoContainer(propsContainer); 
mpc.as(Characteristics.USE_NAMES).addComponent(BigFatComponent.class);
```

Here is another manual use forcing an association between a item in the properties file, and the relative position of a parameter in the constructor. It is most useful when there is not a tie-up between the parameter names and the properties. In this case the arg passed in to the ConfigParameter (varargs list) is the one that matches the property name:

```java
mpc.addComponent(BigFatComponent.class, ConfigParameter("wsURL"), ConfigParameter("serviceTimeout"));
```

Type conversion is automatic in many cases. Properties in a properties file are clearly strings. Parameters can be any of:

-   String
-   All numeric types, boxed as well as primitives
-   java.util.Date
-   java.io.File as well as java.io.URL
-   java.util.Date
-   java.sql.(Date|Time|Timestamp)
-   Any type sporting accessible constructor taking single string parameter.

PropertiesPicoContainer can be part of a tree of containers. It makes sense, of course, being more near the root of that tree.
