---
title: "Dependency Inversion Principle (DIP)"
weight: 4
bookToc: true
---

# The Dependency Inversion Principle

## What Is the Dependency Inversion Principle?

The Dependency Inversion Principle (DIP) states that

- **High-level modules should not depend on low-level modules. Both should depend on "abstractions".**
- **"Abstractions" should not depend on implementation details. Implementation details should depend on "abstractions".**

It is very common in traditional software development for high-level modules to depend on low-level modules.
Take the common three-tier web architecture as an example: the Controller class depends on the Service class, and the Service class depends on the Repository class.

{{< mermaid >}}
flowchart LR
Controller --> Service
Service --> Repository
{{< /mermaid >}}

In contrast, the structure of a well-designed object-oriented program "inverts" the dependency structure familiar from traditional procedural programming.

What does it mean for a high-level module to depend on a low-level module?
It is the high-level modules that contain the important policy decisions and business models of the application and that define why the application exists.
Yet if high-level modules depend on low-level modules, changes to the low-level modules directly affect the high-level modules and force them to change as well.
That is a situation to be avoided at all costs.

Since it is the high-level modules that set the policy of the application,
it makes sense for the high-level modules to influence the low-level modules that handle the implementation details.
Therefore, the high-level modules responsible for business rules must be independent of, and take precedence over, the low-level modules that handle implementation details.
In no case should a high-level module depend on a low-level module.

## Layering

{{< mermaid >}}
flowchart LR
Controller --> Service
Service --> Repository
{{< /mermaid >}}

Look again at the diagram of the three-tier web architecture shown earlier.
The high-level Controller layer uses the lower-level Service layer, and the Service layer in turn uses the even lower-level Repository layer.
At first glance, this looks like a reasonable structure.
However, in this structure the high-level Controller layer is affected by changes in every layer below it; it is a **dependency structure through which impact propagates easily**.

A more appropriate layered model is shown below.

{{< mermaid >}}
flowchart BT
    subgraph RepositoryLayer
    Repository --> RepositoryInterface
    end
    subgraph ServiceLayer
    Service --> ServiceInterface
    end
    subgraph ControllerLayer
    Controller --> ControllerInterface
    end
    Repository --> ServiceInterface
    Service --> ControllerInterface
{{< /mermaid >}}

Each high-level layer declares an abstract interface and receives the services it needs through that interface.
The lower-level layers implement these abstract interfaces.

This way, each high-level class uses the lower layer through the abstract interface it declared itself,
so the upper layers no longer depend on the lower layers.

Conversely, it is now the lower layers that depend on the abstract interfaces declared by the upper layers in order to receive services.

## Inversion of Ownership

Note here that **what is "inverted" is not just the dependencies; the ownership of the interfaces is inverted as well**.

Normally, the interface of a utility library is owned by the library itself.
However, when the Dependency Inversion Principle is applied, the client owns the abstract interface, and the library's services can be said to derive from that interface.

Because ownership is inverted, the Controller layer is not affected by changes in the Service or Repository layers.
Moreover, the Controller layer can be reused with any module that implements ControllerInterface.
In other words, "inverting" dependencies produces a more flexible, durable, and mobile structure.

## Depend on "Abstractions"

In a word, the Dependency Inversion Principle can be described as the heuristic "depend on abstractions".
This principle advises that programs should not depend on concrete classes, and that all relationships in a program should terminate at an abstract class or an interface.

- No variable should hold a pointer or reference to a concrete class
- No class should derive from a concrete class
- No method should override a method implemented in its base class

The Dependency Inversion Principle is, in essence, advice based on heuristics like these.
However, these heuristics are bound to be violated at least once in any program.
Someone has to create instances of concrete classes, and the module that creates them ends up depending on those instances.
Also, there is no need to follow these heuristics for concrete classes that rarely change.
If a class does not change frequently and no similar derived classes are created from it, depending on it directly does little harm.

For example, the class that handles strings is a concrete class in most systems. In Java, for instance, the String class handles concrete strings.
This class rarely changes, so it is not a big problem for a program to depend on it directly.

However, the concrete classes implemented as part of an application program usually change frequently.
You must never depend directly on classes like that, which are likely to change frequently.

Separation through interfaces is not perfect, either.
Sometimes the interface of a frequently changing class has to change, and the impact can reach the abstract interface that represents that class.

From the viewpoint that an abstract interface is declared by the client class, that is, that it exists so the client itself can receive the services it needs,
the interface changes **only when the client needs a change**.
Such changes are driven solely by the client, and the classes that implement the abstract interface never affect the client.

## A Simple Example

### Button and Lamp

When one class sends messages to another, "dependency inversion" can be applied easily.
Let's think about it using a button and a lamp as an example.

A Button object can sense the external environment. When a Button object receives a Poll message, it immediately determines whether the user has "pressed" it.
The mechanism the Button object uses to sense the external state can be anything, whether a button icon in a GUI or a physical button you can press with your finger.
The Button object simply senses whether the user has activated or deactivated it.

A Lamp object, on the other hand, affects the external environment. When this object receives a TurnOn message, it immediately turns on the light of some light-emitting device,
and when it receives a TurnOff message, it turns that light off. Here too, the actual device that emits the light can be anything, whether an LED or an incandescent bulb.

If we think about a control system like this, it would typically look like the following.

{{< mermaid >}}
classDiagram
class Button {
    poll() void
}
class Lamp {
    turnOn() void
    turnOff() void
}
Button ..> Lamp
{{< /mermaid >}}

In this example, the Button object receives a Poll message, determines whether the button has been pressed, and then simply sends a TurnOn or TurnOff message to the Lamp.

However, this Button class depends directly on the Lamp class, so the Button class is affected by changes to the Lamp class.
Also, if a new Motor object comes along in place of Lamp, you cannot reuse Button to control it.
In this example, the only thing a Button object can control is a Lamp object.

In other words, this design violates the Dependency Inversion Principle, because the high-level policy of the application is not separated from its low-level implementation.

### Finding the Underlying Abstraction

So what is the high-level policy? It is the "underlying abstraction" of the application: the essential part that is not affected when implementation details change.

In the Button and Lamp example, the "underlying abstraction" is relaying the on/off input received from the user to the physical object that emits light.
What on/off sensing mechanism is used, or how the light is turned on, can be implemented with any physical device, right?
In other words, the underlying abstraction is the essential part that is not affected by such things.

The following design improves on the previous one by inverting the dependency on the Lamp object.

{{< mermaid >}}
classDiagram
class Button {
poll() void
}
class ButtonServer {
turnOn() void
turnOff() void
}
class Lamp {
turnOn() void
turnOff() void
}
Button ..> ButtonServer
Lamp ..|> ButtonServer
{{< /mermaid >}}

Here, what Button depends on is an interface called ButtonServer.
Lamp implements this interface, so rather than being depended upon, it depends on the interface.
With this design, Button can control any device that implements the ButtonServer interface.

## A Practical Example

In fact, if you have been reading this guide in order, you have already seen a concrete example of an application that conforms to DIP.
Do you remember [the sample application used in the test double chapter]({{% ref "/docs/softwaretest/testdouble#backend-structure" %}})?

{{< mermaid >}}
classDiagram
class TodoAppController{
TodoAppService todoAppService
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppControllerInterface{
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppService{
TodoAppRepository todoAppRepository
ClockService clockService
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppServiceInterface{
getAllTasks() List~Task~
createTask(NewTask newTask, Instant now) void
finishTask(int taskId, Instant now) void
revertTask(int taskId, Instant now) void
deleteTask(int taskId, Instant now) void
}
class TodoAppRepository{
JdbcTemplate jdbcTemplate
getAllTasks() List~Task~
createTask(NewTask newTask, Instant now) void
finishTask(int taskId, Instant now) void
revertTask(int taskId, Instant now) void
deleteTask(int taskId, Instant now) void
}
class ClockServiceInterface{
now() Instant
}
class ClockService{
now() Instant
}
TodoAppController ..> TodoAppControllerInterface
TodoAppService ..|> TodoAppControllerInterface
TodoAppService ..> TodoAppServiceInterface
TodoAppRepository ..|> TodoAppServiceInterface
TodoAppService ..> ClockServiceInterface
ClockService ..|> ClockServiceInterface
{{< /mermaid >}}

It may look a little complicated, but can you see that the implementation conforms to DIP?

Let's look at the actual source code.

```java
// TodoAppController.java
@RestController
public class TodoAppController {
    @Autowired
    TodoAppControllerInterface todoAppService;

    // concrete implementation
}
```

The TodoAppController class depends on TodoAppControllerInterface and uses the Service through the interface.
The interface that TodoAppController depends on was implemented as follows.

```java
// TodoAppControllerInterface.java
public interface TodoAppControllerInterface {
    List<Task> getAllTasks();
    void createNewTask(NewTask newTask);
    void finishTask(int taskId);
    void revertTask(int taskId);
    void deleteTask(int taskId);
}
```

And as for the Service, TodoAppService is an implementation of TodoAppControllerInterface.

```java
// TodoAppService.java
@Service
class TodoAppService implements TodoAppControllerInterface {
    @Autowired
    TodoAppRepository todoAppRepository;
    
    @Override
    public List<Task> getAllTasks() {
        // concrete implementation
    }

    @Override
    public void createNewTask(NewTask newTask) {
        // concrete implementation
    }

    @Override
    public void finishTask(int taskId) {
        // concrete implementation
    }

    @Override
    public void revertTask(int taskId) {
        // concrete implementation
    }

    @Override
    public void deleteTask(int taskId) {
        // concrete implementation
    }
}
```

The same goes for the Repository. TodoAppService depends on TodoAppServiceInterface and uses the Repository through this interface.
The declaration of the interface it depends on looked like this.

```java
// TodoAppServiceInterface.java
public interface TodoAppServiceInterface {
    List<Task> getAllTasks();
    void createNewTask(NewTask newTask, Instant now);
    void finishTask(int taskId, Instant now);
    void revertTask(int taskId, Instant now);
    void deleteTask(int taskId, Instant now);
}
```

And a concrete implementation class implements that declaration.

```java
// TodoAppRepository.java
@Repository
class TodoAppRepository implements TodoAppServiceInterface {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @Override
    public List<Task> getAllTasks() {
        // concrete implementation
    }

    @Override
    public void createNewTask(NewTask newTask, Instant now) {
        // concrete implementation
    }

    @Override
    public void finishTask(int taskId, Instant now) {
        // concrete implementation
    }

    @Override
    public void revertTask(int taskId, Instant now) {
        // concrete implementation
    }

    @Override
    public void deleteTask(int taskId, Instant now) {
        // concrete implementation
    }
}
```

You can see that no layer depends directly on a concrete implementation; each depends on an interface.

## Summary

In traditional procedural programming, the structure ends up with policy depending on implementation details.
Such a structure is undesirable because the "policy" is affected by changes to the details.

In object-oriented programming, dependencies can be inverted by making both the policy and the implementation details depend on abstractions.
Having this dependency inversion built in is the hallmark of good object-oriented design.
If the dependencies are inverted, the design is object-oriented; if they are not, the design is procedural.

The Dependency Inversion Principle is essential for reaping the many benefits of object-oriented technology.
Applying this principle properly is indispensable for building reusable frameworks.
Because applying this principle completely separates abstractions from implementation details, the code becomes much easier to maintain.

---

Finally, let's look at the Interface Segregation Principle (ISP).

{{% button relref="/docs/softwareDesign/solidPrinciple/isp" %}}Interface Segregation Principle{{% /button %}}
