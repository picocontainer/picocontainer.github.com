---
layout: article
title: WebWork1
---

WebWork 1 is a little old now for a web technology. It has been superseded by WebWork 2 and Struts 2. In its day though, it was the pioneer of decent action frameworks for Java.

To configure PicoContainer to handle the dependency injection of WebWork1 actions, refer to the web.xml below. For WebWork1 there is a slightly different listener to the one defined in the [main page](index.html). Also, instead of using WebWork1's servlet, use ours. Everything else from the standard WebWork1 setup is appropriate.

```xml
<web-app>  
  <display-name>PicoContainer-Web WebWork 1 Demo</display-name>  
  
  <context-param>  
    <param-name>webapp-composer-class</param-name>  
    <param-value>*your web app composer class name*</param-value>  
  </context-param>  
  
  <filter>  
    <filter-name>picoFilter</filter-name>  
    <filter-class>org.picocontainer.web.webwork.PicoActionFactory$ServletFilter</filter-class>  
  </filter>  
  
  <filter-mapping>  
    <filter-name>picoFilter</filter-name>  
    <url-pattern>*.action</url-pattern>  
  </filter-mapping>  
  
  <listener>  
    <listener-class>org.picocontainer.web.webwork.WebWorkPicoServletContainerListener</listener-class>  
  </listener>  
  
  <servlet>  
    <servlet-name>WebWork</servlet-name>  
    <servlet-class>webwork.dispatcher.ServletDispatcher</servlet-class>  
    <load-on-startup>1</load-on-startup>  
  </servlet>  
  
  <servlet>  
    <servlet-name>velocity</servlet-name>  
    <servlet-class>org.picocontainer.web.webwork.WebWorkVelocityServlet</servlet-class>  
  </servlet>  
  
  <servlet-mapping>  
    <servlet-name>WebWork</servlet-name>  
    <url-pattern>*.action</url-pattern>  
  </servlet-mapping>  
  
  <servlet-mapping>  
    <servlet-name>velocity</servlet-name>  
    <url-pattern>*.vm</url-pattern>  
  </servlet-mapping>  
  
  <welcome-file-list>  
    <welcome-file>index.html</welcome-file>  
  </welcome-file-list>  
  
</web-app> 
```

With this design you can use Constructor Injection and three tier container scoping for WebWork1 web applications. Make sure you make and configure your own [WebappComposer](composition.html) implementation.

See [downloads](downloads.html) on how to download the webwork module either using Maven or by downloading full distribution.
We have a sample application. It is a small form-style web-app that shows and list of cheeses, allows you to add and delete from the list. [See the Maven2 source](http://svn.codehaus.org/picocontainer/java/2.x/trunk/web/examples/webwork-webapp/) for it. Here is a screenshot of it running:

![WebWork1 webapp](images/WebWork1_App.jpg "WebWork1 webapp")

[Download](warfiles/pico-webwork1-demo.war) the war file for it (full Java source in WEB-INF/classes).
