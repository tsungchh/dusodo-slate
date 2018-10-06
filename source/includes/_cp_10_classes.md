# Chapter 10 - Classes #

Classes belong to higher levels of code organization.

## Class Organization 
Like a newspapaer article:

* public static constants
* private static variables
* private instance variables
* public functions
* private functions

## Encapsulation 
Utitlity functions should be private but could be protected to be accesible by tests in the same package.

## Classes Should Be Small!
... and then should be smaller than that!.

With functions we count lines, with classes we count responsibilities.

Too many responsibilities => God class.

Avoid to include words like 'Processor, Manager, Super' in classnames => aggregation of responsibilities.

Class **description in 25 words** without "if", "and", "or", "but"...

<aside class="notes">
<a href="http://agilemodeling.com/artifacts/crcModel.htm" >CRC - Candidate, Responsibilities, Collaborators</a>
</aside>

## The Single Responsibility Principle
**Classes should have one responsibility**--**one reason to change**.

Too many of us think that we are done once the program works. We move on the next problem rather than going back and breaking the classes into decoupled units with single responsibilities.

We have to organize system complexity. A developer should know where to look to find things and need only understand the directly affected complexity at any given time.

We want systems composed of many small classes. Single responsibility and single reason to change. They colaborate with a few others to achieve the desired system behavior.

## Cohesion
Small number of instance variables => each method manipulates one or more of those variables. => more variables manipulate => more cohesive a method is.

A class in wich each variable is used by each method is maximally cohesive.

## Maintaining Cohesion Results in Many Small Classes
When classes lose cohesion split them!

Breaking a large function into many smaller functions often gives us the opportunity to split several smaller classes out as well => better organization and more transparent structure.

Do it with tests => write a test suit that verified the precise behavior of the first version. Then tiny little changes, one at a time to get the desired result.


<aside class="notes">
<a href="https://18f.gsa.gov/2016/06/24/5-lessons-in-object-oriented-design-from-sandi-metz/" > OO Design from Sandi Metz</a>
</aside>


## Organizing for Change 

> Need to open the class of AreaCalculator if we need to add another Shape

```java
public class AreaCalculator
{
    public double Area(Rectangle[] shapes)
    {
        double area = 0;
        foreach (var shape in shapes)
        {
            area += shape.Width*shape.Height;
        }

        return area;
    }
}

```


> closed it for modification by opening it up for extension

```java
public abstract class Shape
{
    public abstract double Area();
}

public class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }
    public override double Area()
    {
        return Width*Height;
    }
}
```
**Open-Closed Principle** => Our classes should be open for extension but closed for modification.

Allow new functionality via subclassing.

We incorporate new features by extending the system, not by making modifications to existing code.


## Isolating from Change
> Portfolio v.s. TokyoStockExchange v.s. StockExchange
> Benefit: test for Portfolio could be decoupled from real exchange

```java
public interface StockExchange {
  Money currentPrice(String symbol)
}

public Portfolio {
  private StockExchange exchange;
  public Portfolio(StockExchange exchange) {
    this.exchange = exchange;
  }
}

```

**Dependency Inversion Principle (DIP)** => Classes should depend upon abstractions, not on concrete details.

<aside class="notes">
<a href="https://martinfowler.com/articles/dipInTheWild.html"> Reference </a>
</aside>

## DIP (Dependency Inversion Principle) v.s. DI (Dependency injection) v.s. IoC (Inversion of Control)

> method level dependency injection

```ruby
class Player
  def takeATurn(dice)
    dice.roll()
  end
end
```

DI is about how one object acquires a dependency. When a dependency is provided externally, then the system is using DI. Example: 

IoC is about who initiates the call. If your code initiates a call, it is not IoC, if the container/system/library calls back into code that you provided it, is it IoC. Example: ButtonListener

DIP  is about the level of the abstraction in the messages sent from your code to the thing it is calling. To be sure, using DI or IoC with DIP tends to be more expressive, powerful and domain-aligned, but they are about different dimensions, or forces, in an overall problem.

DI is about wiring, IoC is about direction, and DIP is about shape.

