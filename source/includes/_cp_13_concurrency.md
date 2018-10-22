# Chapter 13 Concurrency

## Why Concurrency  ##

- Structre: decouple what from when
- Performance:
  * Daily information aggregator, i/o wait
  * Interative system(UI), request/response
  * Dataset that can be processed parallel

### Myths and Misconceptions ###

- Concurrency always improves performance
  * Only when there is  a lot of wait time an be shread btw threads
- Design does not change when writing concurrency programs
  * Design does change
- Understanding concurrency issues is not important when using Web framework
  * It is important so that we can handle issues like concurrency update and deaklock

## Principles for concurrent code ##

### Keep concurrency-related code separate from other code ###
- Concurrency design is complex enough to be a reason to change
- Single responsibility principle

### Limit the scope of Data ###
- Limit usage of code that will update shared data

### Use copies of data ###
- Avoid race condition
- Common approachs
  * Read only, so no update
  * Copy each objects, merge each results later in one single thread

### Threas should be as independent as Possible ###
- All local variables
- No lock(no sychronized block)


## Know your library ##

- Use the provided thread-safe collections.
- Use the executor framework for executing unrelated tasks. ?
- Use nonblocking solutions when possible.
- Several library classes are not thread safe.

## Know your Execution Models ##

- Three common patterns:
  * Producer-Consumer
  * Reader-Writer
  * Dining Philosohers
- Learn these basic algorithms and understand their solutions

## Beware Dependencies between Synchronized Methods ##

- Avoid using more than one method on a shared object
- If can't avoid, then:
  * Client-Based Locking 
  * Server-Based Locking 
  * Adapted Server

## Testing Threaded Cdoe ##

- Treat Spurious Failures as Candidate Threading Issues, dont ignore them!
- Get your nonthreaded code working first
- Make your threaded code pluggable
- Make your Threaded code tunable
- Run with more threads than processors, to make sure system switches between tasks
- Run on Different platforms
- Instrument your code to try and force failures
  * Hand-Coded
  * AUtomated using AOP


> Hand-Coded

```java

public synchronized String nextUrlOrNull() { 
  if(hasNext()) {
    String url = urlGenerator.next(); 
    Thread.yield(); // inserted for testing. 
    updateHasNext();
    return url;
  }
  return null; 
}

```

> Automated

```java

public class ThreadJigglePoint { 
  public static void jiggle() { 
  }
}

public synchronized String nextUrlOrNull() { 
  if(hasNext()) {
    ThreadJiglePoint.jiggle(); 
    String url = urlGenerator.next(); 
    ThreadJiglePoint.jiggle(); 
    updateHasNext(); 
    ThreadJiglePoint.jiggle(); 
    return url;
  }
  return null; 
}

```


