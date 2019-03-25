---
title: "SOLID principle 2 - Open closed"
last_modified_at: 2018-03-27T21:00:00+01:00
header: 
  overlay_image: /assets/images/post/007-ocp/bg-post.jpg
classes: wide
excerpt: "Code should be extended not modified"
categories:
  - OOP
tags:
  - Java
  - Architecture
---

The second of the **SOLID** principles is the Open Closed Principle and it goes as follows:

> A software artifact should be open for extension but closed for modifications
>
> <cite>Bertrand Meyer</cite>

Basically this tells us that we should **write code that does not need to change each time requirements change**.

To better understand it, let's imagine having a system that keeps track of our finances, allowing us to check all of our transactions at an ATM.
The result is a list of transactions printed out on a piece of paper with a `-` in front of the negative numbers.

We could be then tempted to write a class like this one:

{% highlight java %}
public class TransactionsController {
    private SQLDatabase db;
    
    public TransactionsRenderer(SQLDatabase database) {
        db = database;
    }
    
    public void printTransactions() {
        final List<Transaction> transactions = db.getTransactions();
        for (Transaction transaction: transactions) {
            System.out.println(transaction.balance);
        }
    }
}
{% endhighlight %}

Then one day innovation strikes and now the system should be available for desktop computers as well, but now instead of presenting the user with a piece of paper the transactions are shown onto a screen and the positive ones are colored in green and the negative ones in red (without the minus in front).

This means that the system should be changed to something along these lines:

{% highlight java %}
public class TransactionsController {
    private SQLDatabase db;
    private Renderer renderer;
    
    public TransactionsRenderer(SQLDatabase database, Renderer r) {
        db = database;
        renderer = r;
    }
    
    public void printTransactions() {
        final List<Transaction> transactions = db.getTransactions();
        for (Transaction transaction: transactions) {
            if (r == Renderer.ATM) {
                System.out.println(transaction.balance);
            } else if (r == Rendered.WEBSITE) {
                int balance = transaction.balance;
                if (balance < 0) {
                    // Render it on to screen removing the `-`sign and colour it red
                } else {
                    // Render it on to screen colouring it green
                }
            }
        }
    }
}
{% endhighlight %}

Now, apart the introduction of the class Renderer, specifying which output is the user interacting with, you can see that our system was pretty bad, because when faced with a change it failed on being closed to modification, therefore also open for extension.

# Solution

A good way to look at this problem is to make a schema of the system and divide it into two parts, one that we can refer to as business logic which will perform the calculations and manage the transactions, and another concerned with user representation that will change for every channel the stakeholders will want to implement.

{: style="text-align:center"}
![schema!][schema]

From this diagram we can see that **the arrows point towards the component that we want to protect from change**.
In fact, we do not want our logic to be dependent on changes on the representation level.

Now the classes that could spawn from this new structure are:

{% highlight java %}
public interface FinancialView {
    public void render();
}

public interface Presenter {
    public void present();
}

public class AtmView implements FinancialView {
    @Override
    public void render() {
        System.out.println(transaction.balance);
    }
}

public class WebView implements FinancialView {
    @Override
    public void render() {
        if (balance < 0) {
            // Render it on to screen removing the `-`sign and colour it red
        } else {
            // Render it on to screen colouring it green
        }
    }
}

public class AtmPresenter implements Presenter {
    private FinancialView view;
    
    public AtmPresenter(AtmView v) {
        view = v;
    }
    
    @Override
    public void present() {
        view.render();
    }
}

public class WebPresenter implements Presenter {
    private FinancialView view;
    
    public WebPresenter(WebView v) {
        view = v;
    }
    
    @Override
    public void present() {
        view.render();
    }
}
{% endhighlight %}

Sure it results in more code, but it will take short time to see why this will solve many issues and prevent headaches in the future.

**I used interfaces here to make it more robust, but the main point of the OCP is to work at an architectural level and separate functionalities based on the how, why and when things change, making sure that higher level components are blind to changes to lower level components.**

In the following posts I will discuss the next principle highlighted by the SOLID acronym, the **Liskov substitution principle**

[schema]: https://kioli.github.io/assets/images/post/007-ocp/schema.jpg