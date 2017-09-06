---
layout: article
name: Converters
---

## Overview

<span style="font-weight: normal;;">Converters take string instances in PicoContainer and convert the type that a particular component requires for injection.</span> DefaultPicoContainer has built in converter <span style="font-weight: normal;;">for primitives:</span>

-   Integer
-   Double
-   Boolean
-   Long
-   Float
-   Character
-   Byte
-   Short
-   File

## Integration Example

Here's a test case demonstrating a simple usage of converters

```java
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
```

## Custom Converters

You can extend BuiltInConverter, or implement the Converter interface directly.BuiltInConverter has a method addConverter(..) which you may find convenient:

```java
BuiltInConverters bic = new BuiltInConverters(); bic.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class);
```

Alternatively, you can subclass BuildInConverters:

```java
BuiltInConverters bic = new BuiltInConverters() { 
  @Override 
  protected void addBuiltInConverters() {
    super.addBuiltInConverters(); 
    super.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class); 
  } 
};
```

or:

```java
BuiltInConverters bic = new BuiltInConverters() { 
  public BuiltInConverters() {
    super.addConverter(new SomeCustomThingConverter(), SomeCustomThing.class); 
  } 
};
```

Converters themselves are easy to write.Here's the built in one for boolean:

```java
class BooleanConverter implements Converter<Boolean>{ 
  public Boolean convert(String paramValue) { 
    return Boolean.valueOf(paramValue); 
  } 
}
```

Setup a container to use your converter:

```java
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
```
