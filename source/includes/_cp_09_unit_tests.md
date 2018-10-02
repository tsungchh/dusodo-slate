# Chapter 9 - Unit tests #

The Agile and TDD movements have encouraged many programmers to write unit tests but many of them have missed important points of writing good tests.

## The Three Laws of TDD 
* **First Law**: You may not write production code until you have written a failing unit test.
* **Second Law**: You may not write more of a unit test than is sufficient to fail, and not compiling is failing.
* **Third Law**: You may not write more production code than is sufficient to pass the currently failing test.

<aside class="notes">
Cycle of 30 secs long. tests should be written a few secs ahead of the production code.
</aside>

If we follow these rules we will write dozens of tests per day, hundreds of tests per month and thousands of tests every year. These tests can rival the size of the production code and can present a daunting management problem.

## Keeping Tests Clean 
Dirty tests are equivalent or worse than having no tests! Need to follow the same rules of well-named variables, short & descriptive functions.

Example: When managers asked why their estimates were getting so large, the developers blamed the tests. In the end they were forced to discard the test suite entirely.

Without a test suite:
* We lost the ability to make sure that changes work as expected and do not break other parts of our system.
* We start to fear making changes.
* We stop cleaning our production code => more harm than good.
* => bugs, frustrated customers, the feeling that our testing effort had failed.

**Test code is just as important as production code, its not a second-class citizen**. It requires thought, design and care. Keep it as clean as production code.
## Tests Enable the -ilities

**If you don't keep your test clean, you will lose them**

Unit tests keep our code flexible, maintainable and reusable. Without them every change is a possible bug.

You can improve that architecture and design without fear!

## Clean Tests 

> Example - Without refactor:

```java
public void testGetPageHieratchyAsXml() throws Exception
{
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response = (SimpleResponse) responder.makeResponse(
    new FitNesseContext(root), request
  );
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}
```

> Refactored:

```java
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  );
}
```

**Readability makes clean test.**
Clarity, simplicity and density of expression => say a lot with a few expressions as possible.

**BUILD-OPERATE-CHECK** pattern:
* Build: the first part builds up the test data.
* Operate: the second part operates on that test data.
* Check: the third part checks that the operation yielded the expected results

You can read the test very quickly without being overwhelmed by details.

## Domain-Speciﬁc Testing Language
Functions and utilities that make the test more convenient to write and easier to read.

It requires a continued refactoring.

## A Dual Standard 
The code within the testing API will be simple, succint, expressive but it need not be as **efficient** as production code. It runs in a test environment and it has different needs.

You can write code that may break rules or is not very efficient but is ok if it makes the test easy to understand.

Dual standard: There are things that you might never do in a production environment that are perfectly fine in a test environment.

## One Assert per Test 
This is a good guideline but the best thing we can say is that **the number of asserts in a test should be minimized.**

## Single Concept per Test 
Better than one assert per test.

> Tough to understand

```java

public void turnOnLoTempAlarmAtThreshold() {
  hw.setTemp(WAY_TOO_COLD);
  controller.tic();
  assertTrue(hw.heaterState());
  assertTrue(hw.blowerState());
  assertFalse(hw.collerState());
  ...
}
```

> Better version: hide the details of tic function and use upper case to represent 'on', lowercase for 'off' for each state. (DSL)

```java

public void turnOnLoTempAlarmAtThreshold() {
  wayTooCold();
  assertEquals("HBchL", hw.getState());
}
```

**Best rule:** 
* **You should minimize the number of asserts per concept and test just one concept per test function**

## F.I.R.S.T
* **Fast**: you want to run them fequently.
* **Independent**: should not depend on each other.
* **Repeatable**: in any environment, in your laptop, without network...
* **Self-Validating**: should have a boolean output... not a log that you have to read to know the result.
* **Timely**: Write them before the production code, if you do it after maybe the production code will be hard to test.

## Conclusion
**If you let the test rot, then your code will rot too. Keep your test clean!**
