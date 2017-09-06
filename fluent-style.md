---
layout: article
name: Fluent Style
---

p. PicoContainer allows for a very fluent style of use. Refer to Martin's "'Fluent Interface'":http://www.martinfowler.com/bliki/FluentInterface.html article.

p. A simple example :

{% highlight java %}
pico = new DefaultPicoContainer();
pico.addComponent(Apple.class)
    .addComponent(Orange.class)
    .addComponent(Pear.class) 
    .addComponent(Banana.class) 
    .addComponent(Grape.class, Grenache.class);
{% endhighlight %}

p. One with per component properties:

{% highlight java %}
pico = new DefaultPicoContainer();
pico.addComponent(Apple.class)
    .as(CACHE).addComponent(Orange.class) 
    .as(CACHE, HIDE_IMPLEMENTATION).addComponent(Pear.class) 
    .as(SYNCHRONIZE, CACHE).addComponent(Banana.class) 
    .addComponent(Grape.class, Grenache.class);
{% endhighlight %}

p. One with properties that are persistently set, half way through.

{% highlight java %}
pico = new DefaultPicoContainer();
pico.addComponent("Coxs", CoxsApple.class) 
    .addComponent("Golden", GoldenDeliciousApple.class) 
    .addComponent(new ValenciaOrange()) 
    .change(SYNCHRONIZE, CACHE, HIDE_IMPLEMENTATION) 
    .addComponent(Pear.class) 
    .addComponent(Banana.class) 
    .addComponent(Grape.class, Grenache.class);
{% endhighlight %}

