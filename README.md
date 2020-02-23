# Testing

# Introduction

The following document contains the basics about testing with the Junit framework and Mockito, always oriented towards a day-to-day utilization.

A software test is a piece of software, which executes another piece of software. It validates if that code results in the expected state (state testing) or executes the expected sequence of events (behavior testing).

Running tests automatically helps to identify software regressions introduced by changes in the source code. Having a high test coverage of your code allows you to continue developing features without having to perform lots of manual tests.

# Unit Testing

A unit test is a piece of code written by a developer that executes a specific functionality in the code to be tested and asserts a certain behavior or state. A unit test targets a small unit of code, e.g., a method or a class. External dependencies should be removed from unit tests, e.g., by replacing the dependency with a test implementation or a (mock) object created by a test framework. This can be done using &quot;test doubles&quot;, which can be classified as:

- Dummy Objects: objects assigned to the component but those are never called and are, usually, empty.
- Fake Objects: These objects have simplified functional implementations, they use data that doesn&#39;t come from any data base.
- Stub Classes: Partial implementation of an interface or class with the purpose of being used during the test, normally they only include the methods used in the test.
- Mock Objects: Dummy implementation on an interface os class in which the output of a method is defined.

 The aim of the Mockito libraries is to isolate the objects to be tested from third parties dependencies, which are impractical to incorporate into the unit test.





# Junit

JUnit is a test framework which uses **annotations** to identify methods that specify a test. JUnit is an open source project hosted at Github, available in the following link: [https://github.com/junit-team](https://github.com/junit-team)

# Dependencies

To use Junit with a Maven build, just add the following dependency in your pom.

```
 <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

In order to find the newest version of the dependency, you can search in the following link: [https://search.maven.org/](https://search.maven.org/) the key words: g:&quot;junit&quot;, a:&quot;junit&quot;

# Example

In this example, we assume that the class &quot;MyMathClass&quot; includes a method &quot;multiply&quot; that takes two integers and does a multiplication.



```
 import static org.junit.jupiter.api.Assertions.assertEquals;
 import org.junit.jupiter.api.Test;
 
 public class MyTests { 
    @Test    
    public void multiplicationOfZeroShouldReturnZero() {
        MyMathClass math = new MyMathClass(); //MyMathClass is tested
        // assert statements
        assertEquals(0, math.multiply(10, 0));
        assertEquals(0, math.multiply(0, 10));
        assertEquals(0, math.multiply(0, 0));
        }
     
  }
 ```


The key parts in this code are:

- The initial **annotation** (@Test): it indicates that the following method is a Junit test, and its result will be indicated, as a failure or success. To disable a test, you just add @Ignore before the method, this way we won&#39;t get informed of its result.
- The **assertion** In this part the result of an operation will be evaluated in order to know if the code is working as expected or not. Some examples could be:
  - AssertTrue(condition), AssertFalse(condition): when evaluating conditions or Boolean values.
  - AssertNull(object), AssertNotNull(object)
  - AssertEquals(expected, actual): testing the equality of two objects.
  - AssertArrayEquals(expected, actual): testing the equality of two arrays.

You can check all the assert methods provided by the framework in this link: [https://junit.org/junit4/javadoc/4.12/org/junit/Assert.html](https://junit.org/junit4/javadoc/4.12/org/junit/Assert.html) and in this link, there is a list of the different annotations: [https://junit.org/junit4/javadoc/4.12/org/junit/package-tree.html](https://junit.org/junit4/javadoc/4.12/org/junit/package-tree.html)

# Asserting exceptions

Sometimes we want to check if an exception is thrown in order to handle expected errors in our code.

To indicate that the test _should_ encounter an exception, we must:

1. Add the initial annotation **@Test(expected= Exception.class)**. This is indicating which specific exception the code should throw.
2. Inside the method, include: **fail([message])**. It indicates that the method should, just, fail. It also allows us to inspect the exception.

```
@Test(expected = NullPointerException.class)
public void whenExceptionThrown_thenExpectationSatisfied() {
    String test = null;
    test.length();
    fail();
}
```

# Mockito

Mockito is a mocking framework, JAVA-based library that is used for effective unit testing of JAVA applications. Mockito is used to mock interfaces so that a dummy functionality can be added to a mock interface that can be used in unit testing

# Dependencies

To use Mockito with a Maven build, just add the following dependency in your pom.
```
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>2.0.2-beta</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <scope>test</scope>
</dependency>
```


Search for g:&quot;org.mockito&quot;, a:&quot;mockito-core&quot; via the [https://search.maven.org/](https://search.maven.org/) website to find the correct pom entry.

# Example

The basic structure for test classes is the following:

```
 public class CustomerServiceTest { 
 @Mock    
 private CustomerRepository customerRepo;
 
 @InjectMocks
 private CustomerService customerService;
 
 @Before
 public void init() throws Exception {
 MockitoAnnotations.initMocks(this);
 }
 
 @Test
 public void test() {
 //assertion here
 }}
```


The important parts are the first two annotations and the init() method, annotated with @Before.

- @Mock: this creates a mock implementation of the repository.
- @InjectMocks: it will inject the mocks marked with the previous annotation to the instance that we are creating.

Inside the init() method the instances will be created, thanks to the initMocks() method. Remember that the @Before and @Test annotations are from the JUnit framework.

Note also that every operation contained inside this method will be executed before each test defined, because of the @Before annotation. So, for example, variables are defined inside this method, they will be created each time a test is executed.

### When/ then pattern

The objective of the Mockito libraries is abstracting the tests from third party dependencies. But, we need to indicate the desired behavior of these mocks when certain methods are called. An example could be:

```
final Customer customer = new Customer();
final Customer customerSaved = new Customer();
[…]
@Test
public void test() {
    Mockito.when(customerRepo.save(customer).thenReturn(customerSaved);
    final Customer response = customerService.createCustomer(customer);
    assertEquals(customerSaved, response);
}
```


Here, we are indicating that, when the repository attempts to save the customer object, it should return the other object in order to continue with the method. Note that if the exact same object (customer) is not passed, the mock will not be executed and an exception will be thrown.

Assuming that the createCustomer() method returns the object saved, then we are asserting that the method&#39;s response is the one we are expecting.

Other methods can be called instead of &quot;thenReturn()&quot;:

- thenThrow(): It is used when we want to mock exceptions.
- thenCallRealMethod(): It can be used to call other method when we are calling another.

# Mock exceptions

If we want to simulate an exception, there are two ways of doing it, depending if the method to be mocked has a void return or not. Remember to specify the failure with the corresponding JUnit annotations.

### Non-Void Return Type

We will use the previously explained when().thenThrow() structure, where we indicate the exception to be thrown when the specified method is called:

```
@Test(expected = NullPointerException.class)
public void nonVoidRetunMethodToThrowEx_thenExIsThrown() {
    MyDictionary dictMock = mock(MyDictionary.class);
    when(dictMock.getMeaning(anyString())).thenThrow(NullPointerException.class);
    dictMock.getMeaning("word");
    fail();
}
```


### Void Return Type

In this case, doThrow()is going to be used, which has a slightly different structure:

```
@Test(expected = IllegalStateException.class)
public void voidRetunMethodToThrowEx\_thenExIsThrown() {
MyDictionary dictMock = mock(MyDictionary.class);
Mockito.doThrow(IllegalStateException.class).when(dictMock).add(anyString(), anyString());
dictMock.add("word", "meaning");
fail();
}
```


# Verify

This method is used to check that certain behavior happened while running out tests. It can be useful when testing void methods, or methods which have internal interactions. This is a simple example to introduce the concept.

```
@Test
void test() {
    List<String> mockList = mock(List.class);
    mockList.add("Hello");
    mockList.size();
    Mockito.verify(mockList).add("Hello");
    Mockito.verify(mockList, times(1)).size();
}
```


The first verify will check if the operation has been done. In the second one, it will also check the number of times that a method has been called.

This can be used with services too, so you can check more complex interactions from your code.  Make sure to add the necessary imports to you class in order to use the times() method.

# ArgumentMatchers

In case that we want to mock a response or check that a method is called but we don&#39;t care about the argument, the Mockito ArgumentMatchers can be used.

- any(Object.class): we can specify the type of object to pass or not.
- anyInt(), anyString(), anyLong()…

A complete list of these ArgumentMatchers can be found here: [https://www.javatpoint.com/mockito-argument-matchers](https://www.javatpoint.com/mockito-argument-matchers)

Some examples could be:

```
when(mockFoo.bool(anyString(), anyInt(), any(Object.class))).thenReturn(true);

when(mocklist.get(Mockito.anyInt())).thenReturn(&quot;Mockito&quot;);

when(mocklist.get(Mockito.anyInt())).thenThrow(new RuntimeException(&quot;Error...&quot;));
```

There are other options, such as specifying what to return in case that an argument is greater or less than a certain value, among others. To see more examples check this link: [https://www.journaldev.com/21876/mockito-argument-matchers-any-eq](https://www.journaldev.com/21876/mockito-argument-matchers-any-eq)