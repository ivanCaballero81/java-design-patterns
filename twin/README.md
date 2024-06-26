---
title: Twin
category: Structural
language: en
tag:
    - Decoupling
    - Object composition
    - Performance
    - Resilience
---

## Intent

To provide a way to handle multiple, related classes in a manner that allows them to work together without inheriting from a common base class.

## Explanation

Real-world example

> An analogous real-world example of the Twin design pattern can be found in the relationship between a driver and a driving simulator. Imagine a driver (the first class) and a driving simulator (the second class) that both need to interact with the same set of vehicle controls (steering, acceleration, braking) and receive the same feedback (speed, engine status).
>
> Despite performing similar functions, the driver and the simulator cannot share a common base class because they operate in fundamentally different environments—one in the physical world and the other in a virtual environment. Instead, they are "twinned" to ensure consistent interaction with the vehicle controls and feedback mechanisms. This setup allows improvements or changes to be made to the simulator without affecting the driver and vice versa, maintaining the system's overall flexibility and resilience. 

In plain words

> It provides a way to form two closely coupled subclasses that can act as a twin class having two ends. 

Wikipedia says

> The Twin pattern is a software design pattern that allows developers to simulate multiple inheritance in languages that don't support it. Instead of creating a single class inheriting from multiple parents, two closely linked subclasses are created, each inheriting from one of the parents. These subclasses are mutually dependent, working together as a pair to achieve the desired functionality. This approach avoids the complications and inefficiencies often associated with multiple inheritance, while still allowing the reuse of functionalities from different classes.

**Programmatic Example**

Consider a game where a ball needs to function as both a `GameItem` and a `Thread`. Instead of inheriting from both, we use the Twin pattern with two closely linked objects: `BallItem` and `BallThread`.

Here is the `GameItem` class:

```java
@Slf4j
public abstract class GameItem {
  public void draw() {
    LOGGER.info("draw");
    doDraw();
  }
  public abstract void doDraw();
  public abstract void click();
}
```

`BallItem` and `BallThread` subclasses:

```java
@Slf4j
public class BallItem extends GameItem {
  private boolean isSuspended;
  @Setter
  private BallThread twin;

  @Override
  public void doDraw() {
    LOGGER.info("doDraw");
  }

  public void move() {
    LOGGER.info("move");
  }

  @Override
  public void click() {
    isSuspended = !isSuspended;
    if (isSuspended) {
      twin.suspendMe();
    } else {
      twin.resumeMe();
    }
  }
}

@Slf4j
public class BallThread extends Thread {
  @Setter
  private BallItem twin;
  private volatile boolean isSuspended;
  private volatile boolean isRunning = true;

  public void run() {
    while (isRunning) {
      if (!isSuspended) {
        twin.draw();
        twin.move();
      }
      try {
        Thread.sleep(250);
      } catch (InterruptedException e) {
        throw new RuntimeException(e);
      }
    }
  }

  public void suspendMe() {
    isSuspended = true;
    LOGGER.info("Begin to suspend BallThread");
  }

  public void resumeMe() {
    isSuspended = false;
    LOGGER.info("Begin to resume BallThread");
  }

  public void stopMe() {
    this.isRunning = false;
    this.isSuspended = true;
  }
}
```

To use these classes together:

```java
var ballItem = new BallItem();
var ballThread = new BallThread();

ballItem.setTwin(ballThread);
ballThread.setTwin(ballItem);
```

This setup allows `BallItem` and `BallThread` to act together as a single cohesive unit in the game, leveraging the capabilities of both `GameItem` and `Thread` without multiple inheritance.

## Class diagram

![Twin](./etc/twin.png "Twin")

## Applicability

* Use when you need to decouple classes that share common functionality but cannot inherit from a common base class due to various reasons such as the use of different frameworks or languages.
* Useful in performance-critical applications where inheritance might introduce unnecessary overhead.
* Applicable in systems requiring resilience through the ability to replace or update one of the twins without affecting the other.

## Known Uses

* User interfaces where different frameworks are used for rendering and logic.
* Systems integrating legacy code with new implementations where direct inheritance is not feasible.

## Consequences

Benefits:

* Reduces coupling between classes, promoting modularity and easier maintenance.
* Improves flexibility and reuse of classes across different frameworks or languages.
* Enhances performance by avoiding the overhead associated with inheritance.

Trade-offs:

* Can lead to code duplication if not managed properly.
* Increased complexity in managing the interaction between twin classes.

## Related Patterns

* [Adapter](https://java-design-patterns.com/patterns/adapter/): Both patterns deal with compatibility issues, but Adapter focuses on converting interfaces while Twin deals with class collaboration without inheritance.
* [Bridge](https://java-design-patterns.com/patterns/bridge/): Similar in decoupling abstraction from implementation, but Twin specifically avoids inheritance.
* [Proxy](https://java-design-patterns.com/patterns/proxy/): Manages object access, similar to how Twin handles interaction, but Proxy typically focuses on control and logging.

## Credits

* [Design Patterns: Elements of Reusable Object-Oriented Software](https://amzn.to/3w0pvKI)
* [Java Design Patterns: A Hands-On Experience with Real-World Examples](https://amzn.to/3yhh525)
* [Patterns of Enterprise Application Architecture](https://amzn.to/3WfKBPR)
* [Twin – A Design Pattern for Modeling Multiple Inheritance - Hanspeter Mössenböck](http://www.ssw.uni-linz.ac.at/Research/Papers/Moe99/Paper.pdf)
