---
layout: article
name: Unprocessed Properties
---

p. Properties are a powerful mechanism to add hints at registration time as to what the behavior of a component should be. It only works though when the right component factories are set for the container. Ones that recognize the property and can process it. For example, DefaultPicoConstructor's default constructor sets up an adaptive ComponentFactory that can handle HIDE_IMPL:

pico = new DefaultPicoContainer();
 pico.as(HIDE_IMPL).addComponent(Apple.class);But with a custom ComponentFactory, it may not:pico = new DefaultPicoContainer(new MyComponentFactory);
 pico.as(HIDE_IMPL).addComponent(Apple.class); // fails with PicoCompositionExceptionThe trick is (in this case) to make sure that the right chain of component factories are setup:pico = new DefaultPicoContainer(new MyComponentFactory().wrap(new AdaptiveBehaviorFactory()));
 pico.as(HIDE_IMPL).addComponent(Apple.class); // fails with PicoCompositionException

p. A component may have many properties as it is added to the container. As each is processed, it is removed from the set. If there are any left after the component has been added the behavior or action that the property suggests has not been setup. Thus a PicoCompositionException being thrown warning of unprocessed properties only happens one or more properties are left - whether they are PicoContainer's own properties or custom ones

