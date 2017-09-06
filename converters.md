---
layout: article
name: Converters
---

h2. Overview

p.  %{font-weight: normal;}Converters take string instances in PicoContainer and convert the type that a particular component requires for injection.% DefaultPicoContainer has built in converter %{font-weight: normal;}for primitives:% 

* Integer
* Double
* Boolean
* Long
* Float
* Character
* Byte
* Short
* File

h2. Integration Example

p. Here's a test case demonstrating a simple usage of converters

{% highlight java %}
public static class ConverterSample { 
  public final int value;
  public ConverterSample(Integer value) {
    this.value = value; 
  } 
} 

@Test 
public void testIntegrationWithConverters() { 
	MutablePicoContainer mpc = new DefaultPicoContainer(); 
	mpc.addComponent("converterParameter","42") 
	   .addComponent(ConverterSample.class, ConverterSample.class, new ComponentParameter("converterParameter")); 
	ConverterSample result = mpc.getComponent(ConverterSample.class); 
	assertEquals(42, result.value); }
{% endhighlight %}
	
h2. Custom Converters

p. You can extend BuiltInConverter, or implement the Converter interface directly.BuiltInConverter has a method addConverter(..) which you may find convenient:

{% highlight java %}
BuiltInConverters bic = new BuiltInConverters(); bic.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class);
{% endhighlight %}

p. Alternatively, you can subclass BuildInConverters:

{% highlight java %}
BuiltInConverters bic = new BuiltInConverters() { 
  @Override 
  protected void addBuiltInConverters() {
    super.addBuiltInConverters(); 
    super.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class); 
  } 
};
{% endhighlight %}

p. or:

{% highlight java %}
BuiltInConverters bic = new BuiltInConverters() { 
  public BuiltInConverters() {
    super.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class); 
  } 
};
{% endhighlight %}

p. Converters themselves are easy to write.Here's the built in one for boolean:

{% highlight java %}
class BooleanConverter implements Converter<Boolean>{ 
  public Boolean convert(String paramValue) { 
    return Boolean.valueOf(paramValue); 
  } 
}
{% endhighlight %}

p. Setup a container to use your converter:

{% highlight java %}
MutableContainer pico = new DefaultPicoContainer() { 
  @Override 
  public ConverterSet getConverter() { 
    return new BuildInConverters() { 
      public BuiltInConverters() {
        super.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class); 
      } 
    } 
  } 
};
{% endhighlight %}