---
layout: article
name: Leveraging Parameter Names
---

p. This is where the type alone is not enough to indicate which dependency should be put one or more constructor args. The following component has a need for two of the same type, with some subtly different characteristics.

{% highlight java %}
public class AppleProcessor {
  private Apple dessertApple;
  private Apple cookingApple;
  public inject(Apple dessertApple, Apple cookingApple) {
    this.dessertApple = dessertApple; 
    this.cookingApple = cookingApple; 
  } 
  // other methods 
} 
pico.addComponent("dessertApple", GoldenDeliciousApple.class);
pico.addComponent("cookingApple", BramleyApple.class);
pico.as(Characteristics.USE_NAMES).addComponent(AppleProcessor.class);
AppleProcessor appleProcessor = pico.getComponent(AppleProcessor.class);
{% endhighlight %}

p. In this case the constructor to AppleProcessor has carefully named parameters - 'dessertApple' and 'cookingApple'. This is picked up on for the two named components GoldenDeliciousApple and BramleyApple also set up in the container.

p. There are two ways for this to work:

* relying on the fact that your component classes have debug info compiled into them.
* post-processing component classes with "Paranamer":http://paranamer.codehaus.org/ before placing them in their jar

