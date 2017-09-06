---
layout: article
name: TransientPicoContainer
---

p. ImmutablePicoContainer is wrapper mechanisms for a MutablePicoContainer. The resulting reference is not able to accept mutating method invocations by any means.

p. Here is an example of use:

{% highlight java %}
PicoContainer imPC = new ImmutablePicoContainer(someMutableContainer); // imPC has no addComponent methods nor can it be cast back to MutablePicoContainer
{% endhighlight %}