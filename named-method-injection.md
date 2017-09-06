---
layout: article
name: Named Method Injection
---

h3. Overview

p. This is where a component has an empty constructor with dependencies passed in via setters and matching done via the method name (after 'set'). Matches are against named components.

{% highlight java %}
public class FruitBasket { 
  public void setOrange(String orange) { 
    ... 
  } 
  public void setPear(String pear) { 
    ... 
  } 
  public void setBanana(String banana) { 
    ... 
  } 
  // other methods 
}
{% endhighlight %}

h3. Usage

{% highlight java %}
pico = new DefaultPicoContainer(new NamedMethodInjection();
pico.addConfig("orange" , new Seville());
pico.addConfig("pear" , new Bartlett());
pico.addConfig("Banana" , new Cavendish());
pico.addComponent(FruitBasket.class); // etc 
FruitBasket basket = pico.getComponent(FruitBasket.class);
{% endhighlight %}

p. Yes that's right, there's no constructor needed. It means that for a Unit Test, you cannot simply 'new' the class without some lines of code to populate the dependency fields via setters.

p. The component factory for this is a class *NamedMethodInjection* . It only handles named-method injection for components.

