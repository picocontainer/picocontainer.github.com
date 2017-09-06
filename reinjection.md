---
layout: article
title: Reinjection
---

Reinjection is where an existing instance, that may have been already created by PicoContainer, can have a method called to inject more dependancies.

## Direct Reinjection via 'Reinjector'

Consider the following example of class that could benefit from reinjection:

```java
public class ShoppingCart {  
    private Store store;  
    private User user;  
    public ShoppingCart(Store store, User user) {  
        this.store = store;  
        this.user = user;  
    }  
    public boolean addItemTo(Make make, Model model, int quantity) {  
        Cart cart = store.getCart(user);  
        if (cart.contains(make, model)) {  
            cart.on(make, model).increaseQuantity(quantity);  
        } else {  
            cart.addItem(make, model, quantity);  
        }  
        return true;  
    }  
}
```

The class would be instantiated and have 'store' and 'user' satisfied via constructor injection. Later, perhaps corresponding to a web request, addItemTo could be invoked with relevant parameters. Indeed the addItemTo method could be called many times after instantiation.

And here is a fragment of Java showing reinjection in use:

```java
MutablePicoContainer pico = new TransientPicoContainer();  
pico.addComponent(ShoppingCart.class, myShoppingCartInstance);  
pico.addComponent(Make.class, myMake); // you are more likely to use providers that directly hard code values like this.  
pico.addComponent(Model.class, myModel);  
pico.addComponent(int.class, myQuantity);  
boolean result = (Boolean) new Reinjector(pico).reinject(ShoppingCart.class, "addItemTo");
```

In the above example, the PicoContainer instance (or one of its parents) can inject a Make, a Model and a quantity (int / Integer).

And here the same scenario this time leveraging parameter names:

```java
MutablePicoContainer pico = new TransientPicoContainer();  
pico.as(USE_NAMES).addComponent(ShoppingCart.class, myShoppingCartInstance);  
pico.addComponent("make", myMake); // you are more likely to use providers that directly hard code values like this.  
pico.addComponent("model", myModel);  
pico.addComponent("quantity", myQuantity);  
boolean result = (Boolean) new Reinjector(pico).reinject(ShoppingCart.class, "addItemTo");
```

The above would be most useful if you had ambiguous parameter types like 'addItemTo(String make, String model)'

For reinjection to work, you really need to know the exact method to be injected into. You indicate which method by a reflection method reference (not shown), or by method name (as above). In our case for the method name to work it should not be overloaded in the class. You could also rely on an @Inject annotation, but that only makes sense where there is one method in the class to be used in this way. If you are implicating a reinjection method by name (rather than reflection method reference), and there are more than one matching, then the last one invoked will have it's return value returned to the caller.

Reinjection as a type of regular Injection
------------------------------------------

This is where reinjection is managed by a PicoContainer instance, rather than directly.

```java
public class ShoppingCart {  
    private List<Item> list = new ArrayList<Item>();  
    public void addItemTo(Make make, Model model, int quantity) {  
        list.add(new Item(make, model, quantity));  
    }  
}  
...  
// parent container has a ShoppingCart instance  
Method addItemToMethod = ShoppingCart.class.getMethods("addItemTo", Make.class, Model.class, int.class);  
PicoContainer pico = new TransientPicoContainer(new Reinjection(new MethodInjection(addItemToMethod), parent), parent);  
ShoppingCart cart = pico.getComponent(ShoppingCart.class);
```

The method addItemTo() is called during the getComponent() invocation. This works because the return type is void. A non-void type would cause getComponent() to fail with a class cast exception. If you have that component design need, you need to use the Reinjector directly.
