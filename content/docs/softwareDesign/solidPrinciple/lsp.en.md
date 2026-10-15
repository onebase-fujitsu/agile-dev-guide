---
title: "Liskov Substitution Principle (LSP)"
weight: 3
bookToc: true
---

# The Liskov Substitution Principle

## What Is the Liskov Substitution Principle (LSP)?

The Liskov Substitution Principle (LSP) states that
**subtypes must be substitutable for their base types**.
What happens if we do not follow this principle?

Suppose a function f takes as its argument a pointer or reference to some base class B.
Now imagine there is a class D derived from B, and passing it to f in place of B causes f to misbehave.
In that case, we say that class D violates the Liskov Substitution Principle.

## Examples That Violate the Liskov Substitution Principle

### A Violation Caused by Not Deriving from Shape

If you try to write code that does not conform to the Liskov Substitution Principle, you end up relying on runtime types.
And relying on runtime types results in source code that violates the Open-Closed Principle.

Let's look at a concrete example.

```C++
struct Point {double x,y;};
struct Shape {
    enum ShapeType {square, circle} itsType;
    Shape(ShapeType t) : itsType(t) {}
};

struct Circle : public Shape {
    Circle() : Shape(circle) {};
    void Draw() const;
    Point itsCenter;
    double itsRadius;
};

struct Square : public Shape {
    Square() : Shape(square) {};
    void Draw() const;
    Point itsTopLeft;
    double itsSide;
};

void DrawShape(const Shape& s) {
    if(s.itsType == Shape::square) {
        static_cast<const Square&>(s).Draw();
    else if(s.itsType == Shape::circle) {
        static_cast<const Circle&>(s).Draw();
    }
}
```

This `DrawShape()` clearly violates OCP.
`DrawShape()` has to know about every class derived from Shape,
and it has to be changed every time a new subtype of Shape is created.

Here, both Circle and Square have their own `Draw()`.
However, these functions do not override a function of Shape, so naturally Square and Circle cannot be used in place of Shape.
Therefore, DrawShape has to identify the type of the Shape it receives and call the appropriate `Draw()`.

The violation of the Liskov Substitution Principle (LSP) is that Square and Circle cannot be used in place of Shape.
Because of that, DrawShape() also violates the Open-Closed Principle.
**A violation of the Liskov Substitution Principle inevitably leads to a violation of the Open-Closed Principle as well.**

### The Square and Rectangle Example

The previous example was a very obvious violation, so let's look at a subtler one.

```C++
class Rectangle {
    public:
        void SetWidth(double w) {
            itsWidth = w;
        }
        void SetHeight(double h) {
            itsHeight = h;
        }
        double GetHeight() const {
            return itsHeight;
        }
        double GetWidth() const {
            return itsWidth;
        }
    private:
        Point itsTopLeft;
        double itsWidth;
        double itsHeight;
};
```

Suppose we have an application that works with a Rectangle class like this.
Then one day, a request comes in to handle squares as well as rectangles.

Inheritance is said to be an IS-A relationship ("X is a Y").
In other words, if a new object and an old object satisfy an IS-A relationship, the new object should be derived from the old one.

Of course, a square is a kind of rectangle, so it is perfectly natural to consider the Square class as derived from the Rectangle class.

{{< mermaid >}}
classDiagram
    Square --|> Rectangle
{{< /mermaid >}}

Now we have a problem.
Square does not need both itsWidth and itsHeight; it needs only one of them.
Even so, Square pointlessly inherits all of Rectangle's member variables.
On top of that, Square also inherits functions such as setWidth and setHeight from Rectangle.
Since the width and height of a square are always the same, these functions are not appropriate for Square.

As a way to solve this, what if we override SetWidth() and SetHeight() as follows?

```C++
void Square::SetWidth(double w) {
    Rectangle::SetWidth(w);
    Rectangle::SetHeight(w);
}

void Square::SetHeight(double h) {
    Rectangle::SetHeight(h);
    Rectangle::SetWidth(h);
}
```

This way, when you set the width of a Square object, its height changes to match, and likewise, when you set the height, its width changes.
So we can say the essence of Square is preserved.

However, consider the following function.

```C++
void f(Rectangle& r) {
    r.SetWidth(32);     // Rectangle::SetWidth is called
}
```

What happens if we pass a reference to a Square object to this function?
Here, Rectangle::SetWidth is called, so the height does not change.
In other words, function f does not behave correctly for a subtype of Rectangle, which means it violates the Liskov Substitution Principle.

The cause is that SetWidth and SetHeight are not declared virtual in the base class Rectangle,
so the polymorphism mechanism does not kick in.

In most cases, when creating a derived class affects its base class, it suggests that the original design was flawed.
Having to modify the base class because you created a derived class is nothing less than a violation of the Open-Closed Principle.
Not making setWidth and SetHeight virtual functions was clearly a design flaw.

Let's fix that first.

```C++
class Rectangle {
    public:
        virtual void SetWidth(double w) {
            itsWidth = w;
        }
        virtual void SetHeight(double h) {
            itsHeight = h;
        }
        double GetHeight() const {
            return itsHeight;
        }
        double GetWidth() const {
            return itsWidth;
        }
    private:
        Point itsTopLeft;
        double itsWidth;
        double itsHeight;
};

class Square : public Rectangle {
    public:
        virtual void SetWidth(double w)
        virtual void SetHeight(double h)
};

class Square::SetWidth(double w) {
    Rectangle::SetWidth(w);
    Rectangle::SetHeight(w);
}

class Square::SetHeight(double h) {
    Rectangle::SetHeight(h);
    Rectangle::SetWidht(h);
}
```

Now Square and Rectangle work without problems.
Even if you pass a Square to a function that takes a pointer or reference to a Rectangle as its argument, the Square will behave properly as a square.

So, does this design work correctly without contradictions?

The answer is no, because even if a design is self-consistent, that does not guarantee it will be consistent from the user's point of view.
Consider the following function g.

```C++
void g(Rectangle& r) {
    r.SetWidth(5);
    r.SetHeight(4);
    assert(r.Area() == 20);
}
```

This function accesses SetWidth and SetHeight in the belief that they are member functions of Rectangle.
The function works correctly for a Rectangle, but fails when it receives a Square.
**The author of function g believes that changing the width of a Rectangle does not change its height.**

It is perfectly reasonable to assume that changing the "width" of a rectangle does not affect its "height".
However, not everything passed to function g as a Rectangle necessarily satisfies that assumption.
If you pass an instance of Square to a function like g, which is designed on that assumption, the function will malfunction.
In other words, function g exposes the "fragility" of the inheritance relationship between Square and Rectangle.

The problem with function g is that its author assumes the "height" and "width" of a rectangle are independent of each other.
On the other hand, it is also true that being able to handle "height" and "width" independently is a universal property of a class named Rectangle,
and you could say it was Square that failed to follow that universal property.
Yet Square, for its part, properly follows the properties of a square.

## The "Behavior" That Comes with an "IS-A Relationship"

Square and Rectangle were each clearly correct, so why did the bug occur, and where should we say the problem lies?

Was Square not in an IS-A relationship with Rectangle?
At least for the author of function g, the answer must have been no.
A square may well be a rectangle, but from the point of view of function g, a Square object is definitely not a Rectangle object,
because the behavior of a Square object differs from the behavior of a Rectangle object.

Ultimately, **software is "behavior" itself**.
From this point of view of "behavior", Square is not a Rectangle.

The relationship between Square and Rectangle violated the Liskov Substitution Principle because of this difference in "behavior".
In other words, **to conform to the Liskov Substitution Principle, satisfying an "IS-A relationship" is not enough; you also need to pay attention to the equivalence of "behavior".**

## Heuristics and Conventions

There are simple heuristics for judging whether code conforms to the Liskov Substitution Principle.

- A derived class that removes some functionality of its base class
- A derived class that throws exceptions that its base class does not

Derived classes like these are very likely to violate the Liskov Substitution Principle.

### Degenerate Functions in Derived Classes

For example, consider the following case.

```java
public class Base {
    public void f() {
        // some code
    }
}

public class Derived extends Bass {
    public void f() {}
}
```

Method f is implemented in Base but has been removed in Derived.
Perhaps the author of Derived thought that f had no role to play in Derived.
However, users of Base have no way of knowing that method f has degenerated.

A degenerate function in a derived class does not necessarily violate the Liskov Substitution Principle,
but when you see this kind of situation, it is a good idea to review it carefully.

### Throwing Exceptions from Derived Classes

Another form of violation is adding exceptions to methods of a derived class that the base class does not throw.
If users of the base class do not expect such exceptions, adding new exceptions to the derived class's methods makes it impossible to substitute it for the base class.
You need to either warn users or make sure the derived class does not throw the exceptions.

## Summary

Many things have been said about object-oriented design, but at the core of all of them is the Open-Closed Principle.
When this principle is in effect, applications become much easier to work with, reusable, and robust.
The Liskov Substitution Principle is one of the key enablers of the Open-Closed Principle.

The Liskov Substitution Principle expresses, in terms of the substitutability of subtypes for base types, how to extend a module without modifying it.
The term "IS-A" is too broad to serve as the definition of a subtype.
The true definition of a subtype is nothing other than being "substitutable" for its base type.

---

Next, let's look at the Dependency Inversion Principle (DIP).

{{% button relref="/docs/softwareDesign/solidPrinciple/dip" %}}Dependency Inversion Principle{{% /button %}}
