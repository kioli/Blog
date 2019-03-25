---
title: "MVP with Retrofit2 and RxJava2 - 6"
last_modified_at: 2017-01-14T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Binding the MVP with a Contract"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

Finally the preparation is done and we can aim directly to our app!

For this example we only have one Activity/View (**MyView**), with one Presenter (**MyPresenter**) and one Model (**MyModel**)
Those three entities need to communicate with one another, and this is done via an interface called **MyContract**, containing the functionalities of the Model, the View and the Presenter

{: style="text-align:center"}
![Handshake][handshake]{:height="188px" width="398px"}

# MyContract

{% highlight java %}
public interface MyContract {
   
   interface ShowModel extends MVPModel {
      void getPeople(MyPresenter callback);
   }

   interface MVPMyView extends MVPView {
      void showLoading();

      void hideLoading();

      void requestData(boolean forceRefresh);

      void sendDataToView(List<Person> data);
   }

   interface MVPMyPresenter extends MVPPresenter<MVPMyView> {

      void getListPeople(boolean isGenderMale);

      void callbackForData(@NonNull List<Person> people);
   }
}
{% endhighlight %}

&nbsp;

# MVPMyView Interface

Here we put the methods we want the View to have:

* *show/hide loading()*: acts on the visibility of the progressBar to indicate to the user a network call is taking place

* *requestData()*: called when the user asks for data (i.e. clicking a button)

* *sendDataToView()*: called to give send the retrieved data to the view

&nbsp;

# MVPMyPresenter Interface

Here we put the method we want the Presenter to have

* *getListPeople()*: we have it take a boolean parameter for the refresh capability of the app in case the user desires new people names

* *callbackForData()*: method with a list of people as parameter that is used as callback between the **Presenter** and the **Repository**

&nbsp;

# MVPMyModel Interface

Here we put the method we want the Model to have

* *getPeople()*: this shifts the reponsibilities to get the people from the **Presenter** to the **Model** since the first should concern itself only with shaping up data for the **View** whereas the **Model** is responsible to handle data directly.

&nbsp;

# Retaining Presenters

A very important thing to remember when dealing with callbacks on threads different from the UI thread, is to handle the case when the place they were originated from is not there anymore, and how to make so that the result arrives to the right place and it's not lost.

In the case of our MVP we can do it specifying a singleton class called PresenterHolder, which function is to retain the instance of the presenters during events like the screen rotation or similar

{% highlight java %}
public class PresenterHolder {
    private static PresenterHolder instance = null;
    private Map<Class, Presenter> presenterMap;

    public static PresenterHolder getInstance() {
        if (singleton == null) {
            synchronized (PresenterHolder.class) {
                if (singleton == null) {
                    singleton = new PresenterHolder();
                }
            }
        }
        return singleton;
    }

    // private constuctor for the singleton pattern
    private PresenterHolder() {
        presenterMap = new HashMap<>();
    }

    public void putPresenter(Class c, Presenter p) {
        presenterMap.put(c, p);
    }

    public <T extends Presenter> T getPresenter(Class<T> c) {
        return (T) presenterMap.get(c);
    }

    public void remove(Class c) {
        presenterMap.remove(c);
    }
}
{% endhighlight %}

In our case from the onSaveInstanceState method in the Views we call putPresenter, storing it on rotation, and then it's restored calling getPresenter on the view creation

Next topic will be [creation of the main activity/view][next]

[next]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part6/
[handshake]: https://kioli.github.io/assets/images/post/001-mvp/handshake.jpg