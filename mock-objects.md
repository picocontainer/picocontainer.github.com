---
layout: article
title: Mock Objects
---

If you have had it with codebases that drag along everything and the kitchen sink, (possibly using the [Singleton](antipatterns/singleton-antipattern.html) ) you must read this page. Classes that look up or instantiate heavyweight classes themselves are such beasts. As you might have experienced, they are a pain to test. (And ample proof that the authors didn't follow TDD ![](/images/thumbs_down.gif) ). Dependency Injection with PicoContainer and Mock Objects to the rescue!

Mock Objects are special objects used during testing. Mock Objects and classes that honour [Constructor Injection](injection.html) are a **perfect match** , since such classes can be handed mocks during testing and"the real thing"when the application is run.

This illustration shows how:

|Unit-Test Time||Deployment Time||
| ![](/images/mock-needs-stuff.png) || ![](/images/pico-needs-stuff.png) ||
h3. The class

Here is what NeedsStuff might look like:

```java
public class NeedsStuff { // These are both interfaces. 
  private final BlueStuff bs;
  private final GreenStuff gs;
  public NeedsStuff(BlueStuff bs, GreenStuff gs) {
    this.bs = bs; 
    this.gs = gs;
  }
  public String doIt() { 
    return bs.jump() + gs.beatIt(); 
  } 
}
```

During test time we'll give NeedsStuff some mocks.

During prime time (when the final application is running), the NeedsStuff class will be instantiated with a SuperHeavyBlueStuff and a NuclearGreenStuff instance. These require some really heavy infrastructure such as database connections and network access. We don't want to drag along that when we test NeedsStuff! (It can wait till the integration test).

### Test Time

Our test becomes like this:

```java
public class NeedsStuffTestCase extends junit.framework.TestCase { 
	public void testNeedsStuffDoesStuff() { 
		BlueStuff bs = createBlueStuffMockThatReturnsBlahOnJump(); 
		GreenStuff gs = createGreanStuffMockThatReturnsHuhOnBeatIt(); 
		NeedsStuff ns = new NeedsStuff(bs, gs); 
		assertEquals("BlahHuh", ns.doIt()); // verify mocks. 
	} 
}
```

We are testing the doIt() method without having to drag along any heavy dependencies ![](/images/smile.gif)

![](/images/information.gif) We won't go into further technical details about mocks, as there are many libraries to choose from. Check out [JMock](http://www.jmock.org/) and [EasyMock](http://www.easymock.org/) , or read about the ideas at [MockObjects](http://www.mockobjects.com/)

### Prime Time

It is left to PicoContainer to instantiate NeedsStuff. In order for it to succeed, we must also configure the container with some real BlueStuff and GreenStuff:

```java
public class AppBootstrapper { 
  public void runapp() {
    pico = new DefaultPicoContainer();
    pico.addComponent(NeedsStuff.class);
    pico.addComponent(SuperHeavyBlueStuff.class);
    pico.addComponent(NuclearGreenStuff.class); 
  } 
}
```

![](images/information.gif) It is even possible to do this component wiring using a soft scripting language. See [PicoContainer Script](http://www.picocontainer.org/script)
