---
layout: article
name: Unit Tests Use Container
---

## Symptoms

A test-case class is directly instantiating a PicoContainer and registering components within it.

## Causes

Its too easy to use a PicoContainer instance and setup the component to test with it

It goes really bad when you're setting up consequential transitive dependencies For example:

```java
public void testCocktailWithVodkaIsAlcoholic() { 
  DefaultPicoContainer container = new DefaultPicoContainer(); 
  container.addComponent(Cocktail.class, BananaPopsicleCocktail.class); // needed by Cocktail container.addComponent(BananaLiqueur.class);
  container.addComponent(OrangeJuice.class); 
  container.addComponent(PineappleJuice.class); 
  container.addComponent(VanillaVodka.class); // needed by Cocktail's ingredients container.addComponent(LiqueurMaker.class);
  container.addComponent(StolichnayaDistillery.class); 
  container.addComponent(FruitJuiceDistributor.class); 
  Cocktail cocktail = container.getComponent(Cocktail.class); 
  assertTrue(cocktail.isAlcoholic()); 
}
```

## What To Do

For unit tests like this, the class being tested should be instantiated directly by the test. [Mock Objects](/mock-objects.html) should be used to "mock" the dependent objects, i.e. supplying a fake implementation that can have expectations set and verified on it, rather than a real implementation. So, the test becomes:

```java
public void testCocktailWithVodkaIsAlcoholic() { 
  BananaLiqueur bl = mock(BananaLiqueur.class); 
  OrangeJuice oj = mock(OrangeJuice.class); 
  PineappleJuice pj = mock(PineappleJuice.class); 
  VanillaVodka vv = mock(VanillaVodka.class); // set expectations these four according to your mocking framework 
  Cocktail cocktail = new BananaPopsicleCocktail(bl, oj, pj, vv); 
  assertTrue(cocktail.isAlcoholic()); // verify expectations on the four mocks here. 
}
```

The implementation details of creating a mock object and setting and verifying expectations have been left out of the example, as the details depend on which mock object library/technique is used. Search for EasyMock, JMock or Mockito in Google.

## Exceptions

The container has to be instantiated somewhere of course.

### Bootstrappers

A common place to instantiate containers is in some bootstrap class in the application.

## Functional Tests

There may be a requirement to write a high-level "functional" test, in which you wish to test the interactions between a set of real components (not mocks). In this case, you may wish to create a container with the appropriate components registered for your test.
