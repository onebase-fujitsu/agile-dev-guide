---
title: "Interface Segregation Principle (ISP)"
weight: 5
bookToc: true
---

# The Interface Segregation Principle

## What Is the Interface Segregation Principle?

The Interface Segregation Principle (ISP) states that **clients should not be forced to depend on methods they do not use**.
This principle helps slim down "fat" interfaces.
A class with a "fat" interface carries many interfaces, and those interfaces are only partially strongly related to each other.
Looked at from another angle, this means that strongly related interfaces can be grouped together.
Each group of member functions then serves a different client,
because different clients need different member functions.

Clients generally use multiple interfaces, but not all of them are strongly related to each other.
The Interface Segregation Principle argues that rather than cramming all interfaces into a single class,
related interfaces should be grouped and used as abstract base classes.

## Interface Pollution

Consider the following security system. It has a Door object that can be locked and unlocked.
You can also find out whether the door is open or closed.

```java
public abstract class Door {
    public abstract void Lock();
    public abstract void Unlock();
    public abstract boolean IsDoorOpen();
}
```

Because this is an abstract class, clients can use any object that implements this Door interface.

Now let's look at TimedDoor, one of the classes that implements this interface.
A TimedDoor is a door that sounds an alarm when it is left open for too long. To achieve this, TimedDoor communicates with an object called Timer.

```java
public abstract class Timer {
    public abstract void Register(int timeout, TimerClient client);
}

public interface TimerClient {
    void Timeout();
}
```
When an object wants to be warned of a timeout, it calls Timer's Register method.
The arguments are the length of the timeout and a TimerClient, and TimeOut() is called back when the timeout expires.

Now, how can the TimerClient class notify TimedDoor of the timeout?

{{< mermaid >}}
classDiagram
Timer --> "0..*" TimerClientInterface
Door ..|> TimerClientInterface
TimedDoor --|> Door
class TimerClientInterface {
    TimeOut() void
}
{{< /mermaid >}}

The approach shown here forces Door to derive from TimerClient.
This means TimedDoor also derives from TimerClient, which lets TimedDoor register itself and receive the TimeOut message itself.

This approach has problems; in particular, the problem is that the Door class depends on TimerClient.
Not every class derived from Door needs a Timer.
In fact, the Door abstract class shown at the beginning does not need a Timer,
and derived classes of Door that do not need a Timer will not use the TimeOut method (they will degenerate it), which potentially violates the Liskov Substitution Principle.

In addition, applications that use derived classes of Door must always import the definition of TimerClient even if they do not use Timer,
which shows that this approach involves "needless complexity" and "needless repetition".

This is interface pollution. Such symptoms can commonly occur in statically typed languages such as Java and C++.
In this example, the Door interface has been polluted with a method, TimeOut, that it does not really need.
The only reason this method has to be built into Door is to benefit one of Door's subtypes.
If this practice continues, every time a subtype needs a new method, an unnecessary method gets added to the base class.
The interface pollution of the base class then spreads further and further, and the interface becomes "fat".

Also, every time a new method is added to the base class, the derived classes have to implement the added method as well.
One way to avoid this hassle is to give such methods a trivial implementation in the base class and have derived classes do nothing if they do not need them.
However, this technique violates the Liskov Substitution Principle, which causes problems for maintenance and reuse.

## Separate Clients Mean Separate Interfaces

In reality, Door and TimerClient should provide interfaces to completely different clients:
Timer should use TimerClient, and the classes that operate doors should use Door.

### The Backwards Force Applied by Clients on Interfaces

When software has to be changed, it is natural to think about how a change to an interface will affect its users.
For example, if you change the interface of TimerClient, you worry about how that change will affect the users of TimerClient.

Conversely, however, there are cases where users force a change to an interface.

For example, suppose a user of Timer registers more than one timeout.
What happens to TimedDoor in this case? When it detects that the Door has been opened, it sends a Register message to Timer and requests notification of a timeout.
However, suppose the door closes before the timeout, stays closed for a while, and then opens again.
In this case, it needs to request a new timeout before the old timeout expires.
Timer now has two timeout requests, but eventually the first requested timeout expires first
and calls TimedDoor's TimeOut method. In other words, the Door sounds the alarm at the wrong time.

This situation can be fixed with a convention like the one shown below.

```java
public abstract class Timer {
    public abstract void Register(int timeout, int timeoutId, TimerClient client);
}

public interface TimerClient {
    void TimeOut(int timeOutId);
}
```

We introduce a unique code, timeOutId, each time a timeout is registered, and Timer passes the same code as an argument when it calls TimerClient's TimeOut.
That way, TimerClient's subtypes can tell which timeout has responded.

Obviously, this change affects every user of TimerClient.
However, we will accept it here, because the missing timeOutId is clearly an oversight that needs to be fixed.

{{< mermaid >}}
classDiagram
Timer --> "0..*" TimerClientInterface
Door ..|> TimerClientInterface
TimedDoor --|> Door
class TimerClientInterface {
TimeOut() void
}
{{< /mermaid >}}

With a design like this, however, the impact of the fix reaches Door as well.
This design suffers from "rigidity" and "viscosity". It is hard to justify a bug in TimerClient affecting clients that use subtypes of Door that do not use Timer.

## Class Interfaces vs. Object Interfaces

Now let's reconsider this example from the perspective of interface segregation.
The two clients (Timer and the users of Door) use a single object that has two separate interfaces.
This is because the two interfaces manipulate the same data, so they must be implemented in the same object.

So how can we separate the interfaces in accordance with the Interface Segregation Principle when the two must coexist?

The answer is to avoid accessing the object through its own interface, instead using "delegation" or accessing the object through its "base classes".

### Separation Through Delegation

One solution is to create an object that derives from TimerClient and delegates the actual processing to TimedDoor.

{{< mermaid >}}
classDiagram
class TimerClientInterface {
    +TimeOut
}
class DoorTimerAdapter {
    +TimeOut()
}
class TimedDoor {
    +DoorTimeOut
}
Timer --> "0..*" TimerClientInterface
DoorTimerAdapter --|> TimerClientInterface
DoorTimerAdapter --> TimedDoor
TimedDoor ..> DoorTimerAdapter : creates
TimedDoor ..> Timer
TimedDoor --|> Door
{{< /mermaid >}}

```java
public class TimedDoor extends Door {
    public TimerClient getDoorTimerAdapter() {
        return new DoorTimerAdapter(this);
    }
    // implementation omitted
}

public class DoorTimerAdapter implements TimerClientInterface {
    TimedDoor itsTimedDoor;
    
    DoorTimerAdapter(TimedDoor timedDoor) {
        itsTimedDoor = timedDoor;
    }
    
    public void TimeOut() {
        itsTimedDoor.DoorTimeOut();
    }
}
```

When TimedDoor wants to register a timeout request with Timer, it creates a DoorTimerAdapter and registers it with Timer.
When Timer sends a TimeOut message to the DoorTimerAdapter, the DoorTimerAdapter delegates the handling of that message to TimedDoor.

This solution conforms to the Interface Segregation Principle because it prevents coupling between Door clients and Timer.
So even if Timer changes, none of the users of Door are affected.
Also, the interface of TimedDoor does not have to be exactly the same as that of TimerClient,
because DoorTimerAdapter translates the TimerClient interface into the TimedDoor interface.
This makes it a very flexible solution.

However, this approach is not ideal, because a new object has to be created every time a timeout is registered.

### Separation Through Multiple Inheritance

The following shows how to achieve the Interface Segregation Principle using multiple inheritance.

{{< mermaid >}}
classDiagram
class TimerClientInterface {
+TimeOut
}
class TimedDoor {
+TimeOut
}
Timer --> "0..*" TimerClientInterface
TimedDoor --|> TimerClientInterface
TimedDoor --|> Door
TimedDoor ..> Timer
{{< /mermaid >}}

```java
public class TimedDoor extends Door implements TimerClientInterface {
    abstract void TimedOut(int timeOutId);
}
```

Here, TimedDoor inherits from both Door and TimerClientInterface.
Both clients can use TimedDoor, and neither depends on TimedDoor.
Both clients can use the same object through separate interfaces.


## Summary

"Fat" classes can cause harmful coupling between the clients that use them.
When one client forces a change on a "fat" class, all the other clients are affected.

To break this harmful coupling, make each client depend only on the methods it actually uses.
This becomes possible by splitting the interface of the "fat" class into fine-grained groups that fit each client's needs.

Each client-specific interface declares only the methods used by the client or client group that uses it.
If you later want to build a "fat" class, you can inherit all the client-specific interfaces and implement the ones you need.

With this approach, clients can break their dependencies on methods they do not use and remain independent of each other.
