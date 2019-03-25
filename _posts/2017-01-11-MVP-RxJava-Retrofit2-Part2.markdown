---
title: "MVP with Retrofit2 and RxJava2 - 3"
last_modified_at: 2017-01-11T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Core classes for MVP"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

Now that we have the required gradle setup let's start creating the core architecture of the project

# Über-quick introduction

&nbsp;

{: style="text-align:center"}
![MVP][mvpImage]

&nbsp;

MVP (Model — View — Presenter) is an architectural concept aimed to decouple the **view** (Activity/Fragment/Custom view showing things to the user) from the **presenter** (layer handling data for the View) and from the **model** (managing data model).
Furthermore in this example we have also the figure of the **Repository** which acts as the layer where the actual data retrieval happens

This helps a great deal with code clarity, atomic responsibility, project structure and testing (and many more!)
The obtained decoupling is awesome and we can use it to establish the MVP architecture via interfaces for the **view** and the **presenter** in the following way

{% highlight java %}
public interface MVPView {

   Context getContext();
}

public interface MVPPresenter<V extends MVPView> {

   void attachView(V baseView);

   void detachView();
}

public interface MVPModel {
   Scheduler DEFAULT_SUBSCRIBE_ON_SCHEDULER = Schedulers.newThread();
   Scheduler DEFAULT_OBSERVE_ON_SCHEDULER = AndroidSchedulers.mainThread();
}
{% endhighlight %}

&nbsp;

# Base presenter super class
Each presenter in the app will inherit from this abstract class

{% highlight java %}
public abstract class BasePresenter<V extends MVPView> implements MVPPresenter<V> {

   private V view;

   @Override
   public void attachView(V mvpView) {
      view = mvpView;
   }

   @Override
   public void detachView() {
      view = null;
   }

   public V getView() {
      return view;
   }

   protected void checkViewAttached() {
      if (view == null) {
         throw new MvpViewNotAttachedException();
      }
   }
}
{% endhighlight %}

Here we defined the view the specific presenter refers to (**needs to be attached when the view is created and detached when the view is destroyed to avoid memory leaks**)

# Base repository super class
Each repository in the app will inherit from this abstract class

{% highlight java %}
public abstract class BaseRepository {
	private LruCache<String, Flowable> cacheFlowables = new LruCache<>(50);

	protected Flowable cacheFlowable(String symbol, Flowable flowable) {
		Flowable cachedFlowable = cacheFlowables.get(symbol);
		if (cachedFlowable != null) {
			return cachedFlowable;
		}
		cachedFlowable = flowable;
		updateCache(symbol, cachedFlowable);
		return cachedFlowable;
	}

	private void updateCache(String stockSymbol, Flowable flowable) {
		cacheFlowables.put(stockSymbol, flowable);
	}
}
{% endhighlight %}

This base class creates a cache for the flowables emitted by RxJava and caches them for future re-use

I realize this class may raise more questions than it answers, but this is more a post to put in practice what all those technologies/libraries/patterns promise, for specific tutorials on what a **Scheduler** is or what a **Disposable** or **Single** are I suggest reading [this][rxjava] or other posts specific on RxJava/RxAndroid

Next topic will be [creation of core classes for Butterknife and Icepick][next]

[next]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part3/
[rxjava]: http://mttkay.github.io/blog/2013/08/25/functional-reactive-programming-on-android-with-rxjava/
[mvpImage]: https://kioli.github.io/assets/images/post/001-mvp/mvp.png