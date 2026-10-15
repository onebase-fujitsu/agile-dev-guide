---
title: "Single Responsibility Principle (SRP)"
weight: 1
bookToc: true
---

# The Single Responsibility Principle

## What Is the Single Responsibility Principle (SRP)?

The Single Responsibility Principle (SRP) states that **there should never be more than one reason for a class to change**.

For example, consider the design shown below.

{{< mermaid >}}
classDiagram
Computational Geometry Application --> Rectangle
Graphical Application --> Rectangle
Graphical Application --> GUI
Rectangle --> GUI
class Rectangle {
    +void draw()
    +double area()
}
{{< /mermaid >}}

The Rectangle class has two methods: one draws a rectangle on the screen, and the other calculates the area of the rectangle.
Here, two applications use the Rectangle class.

One of them is an application that calculates the geometry of shapes.
It uses the Rectangle class to perform geometric computations on shapes, but it never uses the feature that draws on the screen.

The other application handles graphics. It does some geometry calculations too, but it mainly uses Rectangle to draw rectangles on the screen.
This design violates the Single Responsibility Principle, because the Rectangle class takes on two roles: geometric calculation and drawing on the screen.
It is responsible both for providing a mathematical model of a rectangle's geometry and for rendering a rectangle in the GUI.

Violations of SRP like this cause various problems.

For example, if the application that handles geometric calculations is a console application,
the unnecessary GUI gets linked into it, which wastes link time, compile time, and memory space.

Even worse, if a change to the graphical application forces a change to the Rectangle class,
the geometry application, which has nothing to do with graphics, has to be rebuilt, retested, and reloaded.

To prevent this, split the Rectangle class into two, as shown below, and delegate the geometric computation of Rectangle to a GeometricRectangle class.
That way, even if the way rectangles are filled changes, the application that handles geometric calculations is not affected.

{{< mermaid >}}
classDiagram
Computational Geometry Application --> Geometric Rectangle
Graphical Application --> Rectangle
Graphical Application --> GUI
Rectangle --> GUI
Rectangle --> Geometric Rectangle
class Rectangle {
    +void draw()
}
class Geometric Rectangle {
    +double area()
}
{{< /mermaid >}}

## What Is a Responsibility?

The Single Responsibility Principle defines **"a responsibility = a reason to change"**.
If there is more than one reason to change a class, then that class has more than one responsibility.
However, it is often very hard to tell whether a class has more than one responsibility.

For example, consider the following Modem interface.

```java
interface Modem {
    void dial(String pno);
    void hangUp();
    void send(char c);
    char recieve();
}
```

Most people would judge this to be a reasonable interface,
because the four functions declared here are clearly "modem features".

However, the `dial()` and `hangUp()` methods are responsible for "connection management", while the `send()` and `recieve()` methods are responsible for "data communication".

Should these two functions then be separated as shown below? That **depends on how the application is going to change.**

{{< mermaid >}}
classDiagram
ModemImplementation ..|> DataChannel
ModemImplementation ..|> Connection
class DataChannel {
    +void send(char);
    +char recieve();
}
class Connection {
    +void dial(String);
    +void hangUp();
}
{{< /mermaid >}}

If the connection management part is affected by changes to the application, separating the interfaces like this
lets you avoid unnecessary recompiling and reloading.

On the other hand, if the two responsibilities always change at the same time, there is no need to separate them.
**Separating them would only make the design needlessly complex.**

Look again at the definition of SRP: the Single Responsibility Principle states that **there should never be more than one reason for a class to change**.
In other words, **it is not wise to apply the Single Responsibility Principle when there is no sign of change**.

## Persistent Systems and the Single Responsibility Principle

Suppose we have the following design.

{{< mermaid >}}
classDiagram
Employee --> Persistence Subsystem
class Employee {
    +int calculatePay()
    +void store()
}
{{< /mermaid >}}

This is a clear violation of the Single Responsibility Principle: the Employee class contains both business rules and the persistence system (such as a database).

The persistence system rarely changes, whereas business rules change along the way, so the two must not be coupled.

## Summary

The Single Responsibility Principle (SRP) is one of the simplest principles, but it is also one of the hardest to apply correctly.
As the Modem example shows, we tend to couple multiple responsibilities together at every turn.
Finding coupled responsibilities and separating them is the essence of software design.

---

Next, let's look at the Open-Closed Principle (OCP).

{{% button relref="/docs/softwareDesign/solidPrinciple/ocp" %}}Open-Closed Principle{{% /button %}}
