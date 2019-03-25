---
title: "Parameterized unit test"
last_modified_at: 2017-01-08T11:30:00+01:00
header: 
  overlay_image: /assets/images/post/005-parameterized/bg-post.jpg
classes: wide
excerpt: "Running through sets of data"
categories:
  - Android
tags:
  - JUnit
  - Kotlin
---

We all know that Unit tests are vital for our software projects.

It's thanks to them that we are able to say with certainty *(ok, with a high degree of certainty)* that our codebase does what it is supposed to do.
Moreover their presence allow us to confidently change existing code knowing that if we mess up something, they will let us know by **<span style="color:red">failing</span>**

I strongly agree with the following quote

> legacy code is code without tests
>
> -- <cite>Michael Feathers</cite> 

In fact no matter how well written our code is, without tests we cannot change its behaviour neither quickly nor verifiably.

To be useful, unit tests should cover all possible scenarios for the unit they are testing, which means they should not only go through the happy flow, but they should also test for exceptions (in case there are any), nullability and different range of values.

For this last specific case today I wanted to talk about **Parameterization**, which is a fancy word to say **Running the same test using a series of different input**

To better understand this very useful concept let's imagine we wrote the following Kotlin function (same concept applies to Java as well)

{% highlight java %}
fun algorithm(input: Int): Int =
        if (input <= -10) { -10 } 
        else if (input > -10 && input < 0) { input } 
        else if (input == 0) { throw Exception() }
        else input * 2
{% endhighlight %}

Now, this example, although silly, provides room to see how the approach can differ between parameterized and non parameterized tests

Let's begin unit testing this function the traditional way:

{% highlight ruby %}
class TestAlgorithm {

    @get:Rule
    var thrown = ExpectedException.none()

    @Test
    fun `algorithm with negative input limits it to -10`() {
        val result = algorithm(-15)
        assertEquals(-10, result)
    }

    @Test
    fun `algorithm returns negative input if bigger than -10`() {
        val result1 = algorithm(-9)
        assertEquals(-9, result1)
        val result2 = algorithm(-1)
        assertEquals(-1, result2)
    }

    @Test
    fun `algorithm throws exception if input is 0`() {
        thrown.expect(Exception::class.java)
        val result = algorithm(0)
    }

    @Test
    fun `algorithm returns double of a positive input`() {
        val result = algorithm(1)
        assertEquals(2, result)
    }
}
{% endhighlight %}

This could be a possible test, tackling all possible scenarios that the function could address.

This though has two drawbacks, first is a bit fragmented, and although in this case (for post brevity's sake) it dos not show, imagine having to prepare every test with some mocks and other repetitive operations.
Second, these tests do not account for many other scenarios, like what happens for numbers lower than -15? or between -9 and -1? etc.

Of course we could have written a line for each of these cases and have this class explode... OR, we could try our **parameterized tests**

So how do we go about them?
First of all we need to run the test with the Parameterized class, which is placed before the class

{% highlight ruby %}
@RunWith(Parameterized::class)
class TestAlgorithm
{% endhighlight %}

Then in the constructor of this class we need to put as parameters the values we expect our test to use. 
Just to have it simple we can say that our tests will need an input and a result to be checked for equality.

{% highlight ruby %}
@RunWith(Parameterized::class)
class TestAlgorithm(private val input: Int,
                    private val result: Int)
{% endhighlight %}

Lastly, we want to define the set of values that those parameters are gonna have. Let's just say we want to run the test providing as input the numbers from 1 to 4, and checking their result.
So we will write

{% highlight ruby %}
companion object {
        @JvmStatic
        @Parameterized.Parameters
        fun data(): List<Any> = listOf(arrayOf(1, 2), arrayOf(2, 4), arrayOf(3, 6), arrayOf(4, 8))
}
{% endhighlight %}

In the end you will have a class like this

{% highlight ruby %}
@RunWith(Parameterized::class)
class TestAlgorithm(private val input: Int,
                      private val result: Int) {
    companion object {
        @JvmStatic
        @Parameterized.Parameters
        fun data(): List<Any> = listOf(arrayOf(1, 2), arrayOf(2, 4), arrayOf(3, 6), arrayOf(4, 8))
    }

    @Test
    fun `testing algorithm function`() {
        assertEquals(result, algorithm(input))
    }
}
{% endhighlight %}

And if run this will lead to the same test running 4 times, each of which with a different couple input/result
{: style="text-align:center"}
![result!][result]
Of course this can be expanded to cover also the other cases with negative numbers and with the 0 and the exception it throws (which can be added as one parameter for the test)

Can you come up with a way to do it?
If not you can always take inspiration from [here][here]

[here]: https://gist.github.com/kioli/bd35d211ccee8c4450b9c6f45dc2a505
[result]: https://kioli.github.io/assets/images/post/005-parameterized/result.jpg