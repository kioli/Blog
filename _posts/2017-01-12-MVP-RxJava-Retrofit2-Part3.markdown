---
title: "MVP with Retrofit2 and RxJava2 - 4"
last_modified_at: 2017-01-12T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Butterknife and Icepick"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

Now that we have a sweet MVP-ready architecture, how do we integrate these two libraries for view injection and instance state restoration when dealing with Activities/views?
The simple answer to this question is InjectingActivity (class that will be inherited in the code by all Activities/views)

{% highlight java %}
public abstract class InjectingActivity extends AppCompatActivity implements MVPView {

   @Override
   public void onCreate(final Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(getLayoutResId());
      ButterKnife.bind(this);
      Icepick.restoreInstanceState(this, savedInstanceState);
   }

   @Override
   public void onSaveInstanceState(Bundle outState) {
      super.onSaveInstanceState(outState);
      Icepick.saveInstanceState(this, outState);
   }

   /**
    * Provides the layout resource id
    *
    * @return layout resource id
    */
   @LayoutRes
   protected abstract int getLayoutResId();

   /**
    * Provides the context
    *
    * @return context
    */
   @Override
   public Context getContext() {
      return this;
   }
}
{% endhighlight %}

**Butterknife** is required to bind the activity in the *onCreate*, where **Icepick** takes care of restoring the state of data (and saves it with the call in the *onSaveInstanceState*)

Also, there is a method called *getLayoutResId()* to avoid having to always type *setContentView(…)* [**not really necessary but I like it**]. Also, since this extends the MVPView interface, we also override *getContext()*

&nbsp;

# Butterknife at work

The way **Butterknife works** (for this app, but it has more functionalities you guys can know more about [here][butterknife]) is via an annotation we need to put on each Activity's view

{% highlight java %}
@BindView(R.id.button_ok)
Button buttonOk;
{% endhighlight %}

&nbsp;

# Icepick at work

To have **Icepick** work properly we need to mark the specific data we want to save the state of with the annotation @State as written below

{% highlight java %}
@State
ArrayList<String> data;
{% endhighlight %}

Next topic will be [configuration of Retrofit2][next]

[next]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part4/
[butterknife]: http://jakewharton.github.io/butterknife/