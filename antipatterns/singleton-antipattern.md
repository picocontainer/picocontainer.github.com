----
layout: article
name: Singleton
---

The singleton pattern was described in the GoF [Design Patterns](http://en.wikipedia.org/wiki/Design_Patterns) book. Because of its static nature and global availability, it allows component writers to obscurely reference other components. Overuse makes for bad solutions. Overuse at the enterprise level, it makes for very bad solutions.

We claim that the GoF Singleton pattern is, in fact, quite often an anti-pattern. The downside of the singleton is that there are many transitive dependancies that are not easy to spot. Singletons cannot easily be replaced with [Mock Objects](/mock-objects.html) for the sake of easy unit testing.

With PicoContainer we would replace this with a container *managed single instance* , possibly in a container hierarchy (see [Introduction](/introduction.html) and [Caching](/behaviors.html) ).

Quite often with J2EE solutions, the component model is honored to a degree for the sake of external APIs, but is sidestepped for the sake of the internals of the application. Session beans Foo and Bar are likely to leverage a hairball of singletons to achieve their ends. Systems developed along these lines, rapidly become entangled and unmaintainable. These entangled systems can also be referred to as:

-   Raymen Noodle Design
-   Big Ball of Mud <http://www.laputan.org/pub/foote/mud.pdf>
-   Spaghetti Code

In case its not clear, Singletons as a core feature of a component design are mutually exclusive with [Inversion of Control](/inversion-of-control.html) .
