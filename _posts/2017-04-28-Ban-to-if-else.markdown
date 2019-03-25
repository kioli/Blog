---
title: "Ban to if else"
last_modified_at: 2017-04-28T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/004-if-else/bg-post.jpg
classes: wide
excerpt: "Choose which way to go"
categories:
  - Android
tags:
  - Clean code
---

Since the beginning of our programming career, whether that takes root in self-taught evenings and weekends in dusty basements or messy atticsNaturally everybody reading here, or in boring school/university lessons, we have all started coding from the same few basic steps... and the **if-else** conditional statement is one of them

We all know its basic structure as well as its meaning

{% highlight java %}
if (condition) {
    // code to execute if the condition is met
} else {
    // code to execute if the condition is NOT met
}
{% endhighlight %}

And we know that the **else** part can be used like this or it can be chained with an *if* to make **else if (condition)**.
Unfortunately both uses open up more traps than benefits, and I'll spend the next few minutes trying to show you what I mean

Let's imagine to have, somehwere hidden in our code, a function like this one:

{% highlight java %}
public void doSomethingImportant(final int number, @NonNull final Object object) {
    if (object != null) {
        if (number < 0) {
            number = -number;
        }
        // the number is made positive, phew...
        if (number == 0) {
            // executes if the number is 0
        } else if (number % 2 == 0) {
            // executes if the number is even
        } else (...) {
            ...
        }
    } else {
        Log.e("tag", "uh-oh, the object was null");
    }
}
{% endhighlight %}

Pretty messy isn't it? 
And this is just the tip of the iceberg, think about how would it look if we had checks regarding other parameters and cross conditions with them... pure hell

That is why in my projects I got rid of the if-else satement, for both clarity of mind and of code (not only for myself but for whomever will get to read my code in the future)

And how would it look this structure without any **else**? here it is

{% highlight java %}
public void doSomethingImportant(final int number, @NonNull final Object object) {
    if (object == null) {
        Log.e("tag", "uh-oh, the object was null");
        return;
    }
    final int absNumber = Math.abs(number);
    handleNumber(absNumber, object);
}

public void handleNumber(final int number, @NonNull final Object object) {
    if (number == 0) {
        // executes if the number is 0
        return;
    } 
    handlePositiveNumber(number, object);
}

public void handlePositiveNumber(final int number, @NonNull final Object object) {
    if (number % 2 == 0) {
        // executes if the number is even
        return;
    } 
    handleOddNumber(number, object);
}

public void handleOddNumber(final int number, @NonNull final Object object) {
    ...
}
{% endhighlight %}

Removing the *else* from our first method we achieved a cleared structure, smaller methods with only one responsibility, easier to test and decoupled :)