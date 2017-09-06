---
layout: article
name: Regex Composer
---

A RegexComposer is a [composer](monitors.html#composers) allows for regular expressions to be used in conjunction with making lists of components that match that regular expression.

```java
MutablePicoContainer pico = new DefaultPicoContainer(new ComposingMonitor(new RegexComposer()));
pico.addComponent("apple1", "Braeburn");
pico.addComponent("apple2", "Granny Smith");
pico.addComponent("plum", "Victoria"); 
List apples = (List) pico.getComponent("apple[1-9]"); 
assertEquals("[Braeburn, Granny Smith]", apples.toString());
```

The above example is fed the regular expression in the getComponent(..) invocation, which may be in practical. Here is one where the parameter names of the injectee are used for correlation purposes

```java
public static class NeedsApples {
  private List<String> apples;
  public NeedsApples(List<String> apples) { 
    // correlate param name to regex below 
    this.apples = apples; 
  } 
} 
public static class NeedsPlums {
  private List<String> plums;
  public NeedsPlums(List<String> plums) { 
    // correlate param name to regex below 
    this.plums = plums; 
  } 
} 
... 
MutablePicoContainer pico = new DefaultPicoContainer( 
    new ComposingMonitor(new RegexComposer("apple[1-9]", "apples"), new RegexComposer("plum*", "plums")));
pico.addComponent("apple1", "Braeburn") .addComponent("apple2", "Granny Smith") .addComponent("plumV", "Victoria");
pico.as(USE_NAMES).addComponent(NeedsApples.class) 
    .as(USE_NAMES).addComponent(NeedsPlums.class); 
assertEquals("[Braeburn, Granny Smith]", pico.getComponent(NeedsApples.class).apples.toString()); 
assertEquals("[Victoria]", pico.getComponent(NeedsPlums.class).plums.toString());
```