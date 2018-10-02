# Chapter 8 - Boundaries #

Boundaries between applications, 3rd-party, open source software or apps built by other teams

## Using Third-Party Code

> If we need a Map of Sensors you can create something like:

```java
// Without generics
Map sensors = new HashMap();
Sensor s = (Sensor) sensors.get(sensorId);
// With generics:
Map<Sensor> sensors = new HashMap<Sensor>();
Sensor s = sensors.get(sensorId);
```

> This is not clean code. If you use instances of Map<Sensor>
> in your system if the Map Interface changes you will have to change a lot of references.
> Try to encapsulate any boundary interface like Map inside a the class:

```java
public class Sensor {
  private Map sensors = new HashMap();

  public Sensor getById(String id) {
    return (Sensor) sensor.get(id);
  }

}
```



Natural tension between provider/user of an interface. 
**Provider** => focus on wide audience.
**User** => focus on their particular needs.

Example: java.util.Map
Map is an interface with a lot of methods: clear, containsKey, containsValue, get, isEmpty, put, putAll, remove, size...
It is very flexible, any user can add items of any type to any Map, but as a user, I might only need one or two methods.

<aside class="success">
Always use wrapper to wrap 3rd-party code so the boundaries are clear.
</aside>

<aside class="warning">
Why this is bad?
Passing an instance of `Map<Sensor>` means that there will be a lot of places to fix if the interface of Map changes.
</aside>


## Exploring and Learning Boundaries
**Learning tests** instead of experimenting an trying out the new stuff in our production code, we could write some tests to explore our understanding of the third-party code. The tests focus on what we want out of the API.

<aside class="notes">
Console tests v.s. leanring tests
</aside>
 
## Learning Tests Are Better Than Free
Since you have to learn the 3rd party code anyway, learning tests are an easy way to learn *and* future-proof use of the API. With each release comes a new risk => we run the learning tests to see the changes.

Without these tests the migration is easier, without them we could stay with the old version longer than we should.

## Using Code That Does Not Yet Exist
Sometimes you have boundaries in your system that represents things that had not been designed yet.

You can create your own interface with the idea of how this should work and implement a Fake implementation for testing purposes.

Example: Adapter pattern and use fake adpater for testing.

<aside class="notes">
TDD?
</aside>

## Clean Boundaries 

Good software designs accommodate change without huge investments and rework.
Code at the boundaries needs clear separation and tests that define expectations.
Wrap them or use an Adapter to convert from our perfect interface to the provided interface.
