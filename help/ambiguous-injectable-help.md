---
layout: article
name: Ambiguous Injectable
---

p. PicoContainer tries to determine what to inject into the constructor of each component as it instantiates (or setters/fields for other injection types). Sometimes there can be two or more classes that could equally satisfy a constructor:

pico.addComponent(new Apple("Braeburn"));
 pico.addComponent(new Apple("Granny Smith"));
 pico.addComponent(Pie.class); // needs Apple Pie pie = pico.getComponent(Pie.class); // throws AmbiguousComponentResolutionException citing there are two Apples.p. The best advice is don't setup PicoContainer in a way that would allow ambiguities (just put in one Apple in the case above).

h2. Strategies to solve this

h3. Custom Parameters

p. An alternative strategy would be to use custom parameters (TODO)

h3. Named Parameters

p. One more strategy would be to use named parameters

class Pie { Pie(Apple grannySmith) { // etc } }
...
pico.addComponent("braeburn", new Apple("Braeburn"));
 pico.addComponent("grannySmith", new Apple("Granny Smith"));
 pico.as(USE_NAMES).addComponent(Pie.class); // needs Apple, specifically a Granny Smith. Pie pie = pico.getComponent(Pie.class); // OK this time.p. To use names Parameters, you are going to need Paranamer 1.0 (or above) in the classpath, as well as ASM 3.0 (or above). If you are expecting Pico to work based on parameter names, but its not doing so, it is most likely that you do not have paranamer-1.0.jar in the classpath. See "https://nexus.codehaus.org/content/repositories/releases/com/thoughtworks/paranamer/paranamer/":https://nexus.codehaus.org/content/repositories/releases/com/thoughtworks/paranamer/paranamer/ for versions of paranamer. Also "http://maven.objectweb.org/maven2/asm/asm/":http://maven.objectweb.org/maven2/asm/asm/ for the ASM jar.

p. You also have to make a decision as to when parameter name data is made. There are two choices for this:

# At runtime
# At build time

p. When at runtime, PicoContainer will need to be accompanied by ASM and Paranamer in the classpath. You will also need to compile your Java classes with debug information.

p. When at build time, you will need to use and Ant or Maven fragment to post-process the compiled classes for your app to add the paramaeter name data. At runtime, neither Paranamer nor ASM will be needed. See "http://paranamer.codehaus.org/":http://paranamer.codehaus.org/ - specifically the quick start section pertinent to Ant and Maven.

p. 

p. 

