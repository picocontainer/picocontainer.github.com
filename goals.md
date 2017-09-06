---
layout: article
title: Project Goals
---

PicoContainer's core was designed with some goals in mind:

-   No mandatory dependencies.
-   No mandatory implements/extends/import for component makers.
-   No logging built in (we have a Monitor interface and implementations for Commons-Logging and Log4J etc).
-   No meta-data or scripting requirement.
-   Small jar size.
-   Embeddable by design (no main method entry point).

If someone has a need for PicoContainer that we did not originally envision, or a feature not yet implemented, we will try to accommodate it.

The principle of Lowest Common Denominator (LCD) allows us, for PicoContainer itself, to keep things small, while allowing extensions to deliver the larger visions. Thus, logging, scripting, web-technologies, persistence, are dealt with by extensions of PicoContainer.

Meanwhile, we want teams to be able to use PicoContainer in their project in an unobtrusive way.We're happy for teams to embed it in such a way that its invisible.
