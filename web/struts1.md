Struts 1 (or just 'Struts') has been superseded by Struts 2 in recent years. There is still a huge community for Struts 1 web applications though.
To configure PicoContainer to handle the dependency injection of Struts actions, refer to the web.xml below.
web.xml

```xml
<web-app>  
  <display-name>PicoContainer-Web and Struts1 Webapp</display-name>  
  
  <context-param>  
    <param-name>webapp-composer-class</param-name>  
    <param-value>*your web app composer class name*</param-value>  
  </context-param>  
  
  <filter>  
    <filter-name>picoFilter</filter-name>  
    <filter-class>org.picocontainer.web.struts.PicoActionFactory$ServletFilter</filter-class>  
  </filter>  
  
  <filter-mapping>  
    <filter-name>picoFilter</filter-name>  
    <url-pattern>*.do</url-pattern>  
  </filter-mapping>  
  
  <listener>  
    <listener-class>org.picocontainer.web.PicoServletContainerListener</listener-class>  
  </listener>  
  
  <!-- Action Servlet Configuration -->  
  <servlet>  
    <servlet-name>action</servlet-name>  
    <servlet-class>org.apache.struts.action.ActionServlet</servlet-class>  
    <init-param>  
      <param-name>config</param-name>  
      <param-value>/WEB-INF/struts-config.xml</param-value>  
    </init-param>  
    <load-on-startup>2</load-on-startup>  
  </servlet>  
  
  <!-- Action Servlet Mapping -->  
  <servlet-mapping>  
    <servlet-name>action</servlet-name>  
    <url-pattern>*.do</url-pattern>  
  </servlet-mapping>  
  
  <!-- Welcome File -->  
  <welcome-file-list>  
    <welcome-file>index.html</welcome-file>  
  </welcome-file-list>  
  
</web-app>  
```

## struts-config.xml


Things are different in the struts-config.xml file though - there is a PicoContainer enabled RequestProcessor:

```xml
  <form-beans>  
    <form-bean name="cheeseForm" type="org.picocontainer.web.sample.struts.CheeseForm">  
    </form-bean>  
  </form-beans>  
  
  <action-mappings>  
    <action path="/cheese" type="org.picocontainer.web.sample.struts.CheeseAction" name="cheeseForm" scope="request">  
      <forward name="next" path="/cheese.jsp">  
    </forward></action>  
  </action-mappings>  
  
  <controller processorclass="org.picocontainer.web.struts.PicoRequestProcessor">        
  </controller>
```

With this design you can use Constructor Injection and three tier container scoping for Struts web applications. Make sure you make and configure your own [WebappComposer](composition.html) implementation.
See [downloads](downloads.html) on how to download the struts module either using Maven or by downloading full distribution.

## Example App

We have a sample application. It is a small form-style web-app that shows and list of cheeses, allows you to add and delete from the list. [See the Maven2 source](http://svn.codehaus.org/picocontainer/java/2.x/trunk/web/examples/struts-webapp/) for it. Here is a screenshot of it running:

![](images/Struts1_App.jpg)

[Download the war file](warfiles/pico-struts1-demo.war) for it (full Java source in WEB-INF/classes).
