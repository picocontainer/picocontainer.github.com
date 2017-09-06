---
layout: article
name: Struts2
---

To configure PicoContainer to handle the dependency injection of Struts2 actions, refer to the web.xml below. For a start there is a slightly different listener to the one defined in the main page. Also, instead of using Struts2's servlet, use ours. Everything else from the standard Struts2 setup is appropriate.

```xml
<web-app>  
  <display-name>Struts 2 Webapp</display-name>  
  
  <context-param>  
    <param-name>webapp-composer-class</param-name>  
    <param-value>org.picocontainer.web.sample.ExampleStruts2Composer</param-value>  
  </context-param>  
  
  <filter>  
    <filter-name>picoFilter</filter-name>  
    <filter-class>org.picocontainer.web.struts2.PicoObjectFactory$ServletFilter</filter-class>  
  </filter>  
  
  <filter>  
    <filter-name>strutsFilter</filter-name>  
    <filter-class>org.apache.struts2.dispatcher.FilterDispatcher</filter-class>  
  </filter>  
  
  <filter-mapping>  
    <filter-name>picoFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
  </filter-mapping>  
  
  <filter-mapping>  
    <filter-name>strutsFilter</filter-name>  
    <url-pattern>/*</url-pattern>  
  </filter-mapping>  
  
  <listener>  
    <listener-class>org.picocontainer.web.struts2.Struts2PicoServletContainerListener</listener-class>  
  </listener>  
  
  <welcome-file-list>  
    <welcome-file>index.html</welcome-file>  
  </welcome-file-list>  
  
</web-app> 
```

The Maven example project is [here](http://svn.codehaus.org/picocontainer/java/2.x/trunk/web/examples/struts2-webapp/). Make sure you make and configure your own [WebappComposer](composition.html) implementation. Note that we use a base class for Struts2 applications: Struts2Composer
See [downloads](downloads.html) on how to download the struts2 module either using Maven or by downloading full distribution.
