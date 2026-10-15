---
title: "Open-Closed Principle (OCP)"
weight: 2
bookToc: true
---

# The Open Closed Principle

## What Is the Open-Closed Principle (OCP)?

The Open-Closed Principle (OCP) states that
**software entities (classes, modules, functions, etc.) should be open for extension but closed for modification**.

With a "rigid" design, even a small change to a program affects every module that depends on the changed part.
Applying the Open-Closed Principle lets you keep the impact of such modifications small.

Modules designed according to the Open-Closed Principle have the following characteristics.

1. **They are open for extension**  
This means that the behavior of the module can be extended.
Even when the application's specifications change, you can handle the change by adding new behavior to the module.
In other words, you are free to change what the module does.

2. **They are closed for modification**  
Changing the behavior of the module does not affect the module's source code or binary code at all.
Modules that have already been compiled into binary form, whether link libraries or DLLs, do not need to be touched.

At first glance, these two characteristics seem to contradict each other.
Normally, to extend the behavior of a module, you have to change the module's source code itself.
It is also natural to think that you cannot change the behavior of a module without changing its source code.

So how can you change the behavior of a module without changing its source code?

## Depend on "Abstractions"

In object-oriented languages such as C++ and Java, you can use "abstractions" to express methods whose declarations are fixed but which are not bound to any particular implementation.
In these languages, an "abstraction" is written as an abstract base class, and the methods that are not bound to any particular implementation are implemented in its derived classes.

Taking advantage of this "abstraction" mechanism is effective when designing modules.
If a module depends on a fixed "abstraction", the code can be closed for modification.
With abstractions, you can extend the behavior of a module without modifying its code, simply by adding a new class derived from the abstraction.

{{< mermaid >}}
classDiagram
Client --> Server
{{< /mermaid >}}

In this example, both Client and Server are implemented "concretely".
Because the Client class uses the Server class, if the Client object needs to use a different Server object, the Client class has to be changed.

{{< mermaid >}}
classDiagram
Client --> ClientInterface
Server ..|> ClientInterface
{{< /mermaid >}}

What if we use a design like the diagram above instead?
The ClientInterface class is an abstract class with several abstract member functions.
The Client class uses this abstract class.
However, what the Client class actually uses is an object of the Server class, which derives from the abstract class.
In other words, if the Client object wants to use a new Server class, all you have to do is add another class derived from ClientInterface.
There is no need to change the Client class itself.

## A Concrete Example

### Shape Drawing That Does Not Follow OCP

First, let's look at an implementation that does not follow OCP.

The two structs Circle and Square share only their first data element, `ShapeType itsType;`; their other elements differ.
`ShapeType itsType;` indicates the type of the shape, which is how the program determines whether it is a circle or a square.
The `DrawAllShapes()` method walks through an array of pointers to the structs one by one,
first checking the type and then calling the appropriate function for that type (`DrawCircle()` or `DrawSquare()`).

```C++
// shape.h
enum ShapeType {circle, square};

struct Shape {
    ShapeType itsType;
};
```

```C++
// circle.h
struct Circle {
    ShapeType itsType;
    double itsRadius;
    Point itsCenter;
}

void DrawCircle(struct Circle*);
```

```C++
// square.h
struct Square {
    ShapeType itsType;
    double itsSide;
    Point itsTopLeft;
}

void DrawSquare(struct Square*);
```

```C++
// drawAllShapes.cc
typedef struct Shape *ShapePointer;

void DrawAllShapes(ShapePointer list[], int n) {
    int i;
    for (i=0; i<n; i++) {
        struct Shape* s = list[i];
        switch (s -> itsType) {
            case square:
                DrawSquare((struct Square*)s);
                break;
            case circle:
                DrawCircle((struct Circle*)s);
                break;
        }
    }
}
```

This `DrawAllShapes()` clearly violates the Open-Closed Principle.
When you define a new kind of shape and want to draw it, `DrawAllShapes()` also has to be modified.
In other words, this function is not closed for modification.

This program is just a simple example that only calls the drawing routines for shapes, so adding a new shape requires a change in only one place.
In a real application, however, you would call many kinds of operations, such as moving, scaling, and transforming shapes.
Each of those operations needs branching with a switch statement similar to the one used for drawing.
As a result, adding a single new kind of shape requires changes everywhere such switch-based branching occurs.

There are other problems as well. What changes are needed to add a new kind of shape?

First, the new shape has to be registered in the ShapeType enum.
Because every shape being handled depends on the declaration of this enum, all the shapes have to be recompiled,
and for the same reason, the modules that depend on Shape need to be recompiled too.

So not only do you have to change every switch/case and if/else statement in the source code, you also have to recompile the binary files of every module that uses the Shape struct.
Changing binary files means that not only DLLs and shared libraries but also other binary components have to be relinked.

In the end, the simple act of adding a new shape to the application changes not only the source code of various modules
but also all of the modules' binary files and library components.

This program is also very poorly portable. If you try to use `DrawAllShapes()` from another program,
Square and Circle, which you do not even need, come tagged along as extras.

### Implementing Shape Drawing That Follows OCP

The code below solves the problems of the previous code by following OCP.
Here, we first declare an abstract class named Shape that has just one abstract method, `Draw()`.
Circle and Square are then classes derived from Shape.

```java
public abstract class Shape {
    public abstract void Draw();
}

public abstract class Square extends Shape {
    double itsSide;
    Point itsTopLeft;
}

public abstract class Circle extends Shape {
    double itsRadius;
    Point itsCenter;
}

public class DrawingTool {
    public void DrawAllShapes(List<Shape> shapes) {
        for (Shape shape : shapes) {
            shape.Draw();
        }
    }
}
```

Now, if you want to add a new shape, you just create a new class derived from Shape.
`DrawAllShapes()` does not need to be touched at all, which means that `DrawAllShapes()` conforms to the Open-Closed Principle.

As mentioned earlier, a practical application also needs operations such as moving and transforming.
Even so, adding a new shape is very easy: you just create a derived class for the new shape and implement all the required operations.
There is no need to comb through the application looking for places that need changes.

Portability is also ensured, because no application that uses `DrawAllShapes()` has to deal with unwanted extras.

## Pitfalls

Now, what if the specification for [the previous example](#implementing-shape-drawing-that-follows-ocp) changes so that **all circles must be drawn before any squares**?

`DrawAllShapes()` is not closed against that kind of change.
To handle this change, you would need to change the internal processing of `DrawAllShapes()`: first search the list for Circles and draw them,
then search the list for Squares and draw them in the same way.

## Anticipation and "Natural" Structure

If we had anticipated this kind of specification change, we could have introduced a different "abstraction" to protect ourselves from it.
However, the abstraction used in [the previous example](#implementing-shape-drawing-that-follows-ocp) does not help against this kind of change; it only gets in the way.
In other words, this abstraction was inappropriate.

At first glance, the model using the Shape base class looks perfectly natural.
However, in a system where the drawing order matters more than the type of shape, the Shape-based model turns out not to be natural.

In other words, no matter how "closed" a module is, there can always be changes it cannot be closed against.
**There is no model that is natural in every case.**

Since it is impossible to be perfectly closed against every change, closure has to be strategic.
That is, designers have to choose which kinds of changes they want their design to be closed against.
Designers must guess which kinds of changes are likely to happen frequently, and build abstractions that protect them from those changes.

So how can we guess which changes are likely to happen?
That is where user research and the hypothesis validation process come in.
In agile development, we form hypotheses about what we should develop next.
We interview users and have them try prototypes to decide which features to implement next.
All we can do is use that series of processes to anticipate in advance which changes are likely.

## Explicit Closure

### Taking Ordering Out of the Shapes

Now, what should we do when the requirement `all circles must be drawn before any squares` actually comes up?
Closure is achieved through abstraction. In other words, to close `DrawAllShapes()` against ordering, we need to abstract the ordering.
It seems that we should abstract the ordering so that any kind of ordering can be expressed.

Being able to order means that, given two objects, you can find out which one should be drawn first.
So let's achieve ordering by defining an abstract method named `Precedes` on Shape.

```java
public abstract class Shape {
    abstract void Draw();
    abstract boolean Precedes(Shape shape);
}

public class ShapeComparator implements Comparator<Shape> {
    public int compare(Shape shape1, Shape shape2) {
        if (shape1.Precedes(shape2)) {
            return -1;
        } else {
            retrun 1;
        }
    }
}

public class DrawingTool {
    void DrawAllShapes(List<Shape> shapes) {
        List<Shape> orderedShapes = DrawingOrderSort(shapes);
        for (Shape shape : orderedShapes) {
            shape.Draw();
        }
    }
    
    List<Shape> DrawingOrderSort(List<Shape> shapes) {
        List<Shape> orderedShapes = new ArrayList<Shape>(shapes);
        Collection.sort(orderedShapes, new ShapeComparator());
        return orderedShapes;
    }
}
```

Here, we implemented the ordering by overriding the compare method of the Comparator class.
As it stands, however, each Shape object has to override the `Precedes()` method to specify how it should be ordered.

For example, the `Precedes()` method for Circle would look like this.

```java
public abstract class Circle extends Shape {
    double itsRadius;
    Point itsCenter;

    boolean Precedes(Shape shape) {
        return shape instanceof Square;
    }
}
```

From the explanation so far, it should be obvious that this `Precedes()` violates the Open-Closed Principle.
Of course, this is fine if new shapes are rarely defined, but if they are added frequently, it is crystal clear that this approach will become a problem.

### Using a Table-Driven Approach

Let's close the Shape derivatives so that the individual shapes do not need to know about each other.
Here, we will use a table-driven approach.

```java
public abstract class Shape {
    abstract void Draw();
    abstract String getType();
    private String[] typeOrderTable = {"square", "circle"};
    
    boolean Precedes(Shape shape) {
        String thisType = this.getType();
        String argType = shape.getType();
        
        int thisOrd = -1;
        int argOrd = -1;
        int ord = 0;
        
        for (String tableEntry : typeOrderTable) {
            if (tableEntry.equals(thisType)) {
                thisOrd = ord;
            }
            if (tableEntry.equals(argType)) {
                argOrd = ord;
            }
            if ((0 <= argOrd) && (0 <= thisOrd)) {
                break;
            }
            ord++;
        }
        return thisOrd < argOrd;
    }
}
```

Here, we created an array in Shape that defines the order of the elements.

```private String[] typeOrderTable = {"square", "circle"};```

That is the array.
With this approach, DrawAllShapes can be closed against the general ordering problem,
and in addition, when you create a new class derived from Shape, you no longer need to change the source code of each Shape derivative.

With this approach, the only thing that is not closed against the ordering of the various shapes is this array.
It is a good idea to separate this table from the other modules and make it an independent module of its own.


## Summary

The Open-Closed Principle is at the heart of object-oriented design. By following it, you can make the most of the benefits that object-oriented technology offers (flexibility, reusability, and maintainability).
However, using an object-oriented programming language does not automatically make your code conform to the Open-Closed Principle,
nor does it mean you should use abstractions indiscriminately throughout the application.

Developers need to identify the parts of their code that change most frequently and apply abstractions to them appropriately.
**Refraining from premature "abstraction" is just as important as using "abstraction".**

---

Next, let's look at the Liskov Substitution Principle (LSP).

{{% button relref="/docs/softwareDesign/solidPrinciple/lsp" %}}Liskov Substitution Principle{{% /button %}}
