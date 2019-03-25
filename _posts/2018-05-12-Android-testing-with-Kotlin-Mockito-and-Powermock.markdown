---
title: "Unit testing with Kotlin, Mockito and Powermock"
last_modified_at: 2018-05-12T13:28:00+01:00
header: 
  overlay_image: /assets/images/post/008-testing/bg-post.jpg
classes: wide
excerpt: "Bypass the 'Open class' problem"
categories:
  - Android
tags:
  - Kotlin
  - Testing
  - Mockito
  - Powermock
---

Once we start to code in Android and the dreaded unit tests need to be done, the de-facto standard for mocking components used by our Android classes is definitely gonna be (for the vast majority) the Mockito library.

In this post I'm not gonna explore the usage of such library, but rather I wouldl iketo address a frustrating problem that arises when we test classes written in the marvellous new language Kotlin rather than the old Java.

First of all when we want to adopt Mockito for our unit tests we need to put this line in our `build.gradle` file (at the moment of writing the 2.18.3 was the latest stable version for Mockito)

{% highlight groovy %}
testImplementation 'org.mockito:mockito-core:2.18.3'
{% endhighlight %}

Let's say that we need to unit test this really simple class:

{% highlight java %}
class ClassToTest {
    fun classFunction(message: String, objectX: MyObject) {
        objectX.doObjectAction(message)
    }
}
{% endhighlight %}

We would go ahead and write a test along these lines

{% highlight groovy %}
class MyUnitTest {
    private val mockObject = mock(MyObject::class.java)
    private val classUnderTest = ClassToTest()

    @Before
    fun setup() {
        Mockito.reset(mockObject)
    }

    @Test
    fun classFunctionTest() {
        val inputTest = "Input text"
        classUnderTest.finalFunction(inputTest, mockObject)
        verify(mockObject, atMost(1)).doObjectAction(inputTest)
    }
}
{% endhighlight %}

But running it will result in a failure because 

**Mockito cannot mock/spy because : - final class** 
{: .notice--danger}

This happens because Kotlin has classes and functions `closed` by default, which is the equivalent of the Java keyword `final` that means they cannot be extended by other classes.

Luckily there are a couple of easy workarounds for this:

1) Change our classes and methods to be `open`
This is a pretty bad solution since it changes the code base for testing purposes and having final classes is generically better and safer.

2) Force Mockito mock maker to inline
A better solution is to create a file named `org.mockito.plugins.MockMaker` in the folder `test/resources/mockito-extensions`.

{: style="text-align:center"}
![mockito!][mockito]

Inside this file it's sufficient to write the line **mock-maker-inline** and voilá, now the test is green

3) Change `build.gradle` library to `org.mockito:mockito-inline:2.18.3`
The best solution not to have to add files around

Good, now we are good to go right! **WRONG**

Or at least wrong if we happen to have in our `build.gradle` file also a reference to Powermockito.
In fact, even the simple fact of having that library imported for unit tests, even without actively using it, it's sufficient to mess with the `Mock Maker` and create problems to our tests.

Fortunately the solution exists and it's simple enough also in this case:

It consists on creating a file named `configuration.properties` in the folder `test/resources/org/powermock/extensions`.

{: style="text-align:center"}
![powermock!][powermock]

This time we need to write **mockito.mock-maker-class=mock-maker-inline** in the file and voilá, now the test is green again, and it's here to stay :)

P.s. when you have the Powermock solution in place the previous file introduced in the `test/resources/mockito-extensions` folder can be removed

[mockito]: https://kioli.github.io/assets/images/post/008-testing/mockito.jpg
[powermock]: https://kioli.github.io/assets/images/post/008-testing/powermock.jpg