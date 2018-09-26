# Chapter 6 Objects and Data Structures

## Data Abstraction ##

> Concrete Vehicle

```java
public interface Vehicle {
 double getFuelTankCapacityInGallons();
 double getGallonsOfGasoline();
}
```

> Abstract Vehicle

```java
public interface Vehicle {
 double getPercentFuelRemaining();
}
```

- Hiding implementation is about abstractions. 
- Expose abstract interfaces that allow users to manipulate the `essence` of the data, without knowing the implementation.
- It's not about using getter and setter.

<aside class="success">
Information v.s. data 
</aside>


## Data/Object Anti-Symmetry ##


> OO and Polymorphic style 

```java
public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side*side;
  }
}
```

> procedural style

```java
public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side*side;
  }
}

public class Geometry {
  public double area(Object Shape) throws NoSuchShapeException {
    if (shape instanceof Square) {
      Sqaure s = (Square)shape;
      return s.side * s.side;
    }
    ...
  }
}
```

### Objects vs Data structures ###

- Objects hide their data behind abstractions and expose functions that operate on that data.
- Data structures expose their data and have no meaningful functions.

### OO code vs Procedural code ###

- OO code makes it easy to add new classes without changing existing functions
- Procedural code makes it easy to add new functions without changing the existing data structures.

- OO code makes hard to add new functions because all the classes must change.
- Procedural code makes it hard to add new data structures because all the functions must change.

<aside class="success">
<p> What if we need to add `parameter()` function? </p>
<p> What if we need to add new shape? </p>
</aside>

<aside class="warning">
Don't create Hybrid - half object and half data structure.
</aside>

## The Law of Demeter ##


>Avoid this (called train wreck):

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

> Try to split them, if `ctxt`, `opts`, `scratchDir` are data structure, then exposing their internal structure doesn't violate Demeter.
> If they are objects, knowledge of internal structure violates the Laws of Demeter. What could we do in this case?

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

> Better approach for ctxt: ctx hide its internals and we are not violating Demeters' Law.


```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

loose coupling, principle of least knowledge

A method *f* of a class *C* should only call methods of these:

- C
- An object created by *f*
- An object passed as an argument to *f*
- An object held in an instance variable of *C*

<aside class="warning">
Drawbacks: at the class level, it leads to wide interfaces. it requires introducing many auxiliary methods.
</aside>

## Data Transfer Object ##


