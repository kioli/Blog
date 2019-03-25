---
title: "MVP with Retrofit2 and RxJava2 - 5"
last_modified_at: 2017-01-13T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Retrofit2 configuration"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

I know I know… who would not know the world-famous library from Square that takes the networking mess we need and clears it up organizing it in a simple way for us to use… but still, let's see how Retrofit2 has been set up for this project ;)

Basically there is a class called **ServiceGenerator** where we configure the *RetrofitService* and then we store an instance of it in the **Application** class to be retrieved when needed (it could also be done via Dagger2 but this is out of the scope of these articles)

{% highlight java %}
public class ServiceGenerator {
    
   private static String baseUrl = "http://uinames.com/";
   private NetworkAPI networkAPI;

   public NetworkService() {
      this(baseUrl);
   }

   private NetworkService(String baseUrl) {
      OkHttpClient.Builder httpClient = new OkHttpClient.Builder()
         .addInterceptor(new HttpLoggingInterceptor()
            .setLevel(HttpLoggingInterceptor.Level.BODY));

      GsonConverterFactory gsonConFactory = GsonConverterFactory
         .create(new GsonBuilder()
              .registerTypeAdapterFactory(MyAdapterFactory.create())
              .create());

      final Retrofit retrofit = new Retrofit.Builder()
           .baseUrl(baseUrl)
           .addConverterFactory(gsonConFactory)
           .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
           .client(httpClient.build())
           .build();

      networkAPI = retrofit.create(NetworkAPI.class);
   }

   public NetworkAPI getAPI() {
      return networkAPI;
   }

   public interface NetworkAPI {
      @GET("api/")
      Single<List<Person>> getGenderedNames(
         @Query("gender") String gender);

      @GET("api/")
      Single<List<Person>> getLocalisedNames(
         @Query("region") String region);
   }
}
{% endhighlight %}

It has two constructor to be able to start it off with a custom base URL or the predefined one

When building the service, first it applies an *HttpLoggingInterceptor* to help us log and debug the network calls, then it adds a GSON conversion factory to easily parse the network results into the JSON specified by **MyModel** and finally it creates an implementation of the API endpoints

Also, the API endpoints return a **Single**, which fits very well to be used together with RxJava and the MVP pattern as we will see later on

&nbsp;

# Bonus info: MyAdapterFactory

When/if using **AutoValues** (library from Google for data classes) like we did in the class **Person** things look so nifty

{% highlight java %}
@AutoValue
public abstract class Person implements Parcelable {

   public abstract String name();
   public abstract String surname();
   public abstract String gender();
   public abstract String region();
   public static TypeAdapter<Person> typeAdapter(Gson gson) {
      return new $AutoValue_Person.GsonTypeAdapter(gson);
   }
}
{% endhighlight %}

AutoValue is precious and dear because:

* Acts on abstract classes that will be generated at compile time

* Generates immutable classes

* Generated classes also contains *hashCode()*, *toString()* and *equals()*

* The library comes with nice extensions (for example for Parcelable, Gson, With, etc.)

For a nice understanding of the topic I recommend you read [this great article][autovalue], but here I wanted to tell you about something else…

In order for the network result to be parsed properly to a JSON first we need to define a **TypeAdapter** in the AutoValue class, then to avoid adding each generated TypeAdapter to our Gson instance manually, we can use a common **MyAdapterFactory** class to pass to Retrofit in the method *addConverterFactory()*

{% highlight java %}
@GsonTypeAdapterFactory
abstract class MyAdapterFactory implements TypeAdapterFactory {

   // Method to access the package private generated implementation
   static TypeAdapterFactory create() {
      return new AutoValueGson_MyAdapterFactory();
   }
}
{% endhighlight %}

Next topic will be [binding of the MVP][next]

[next]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part5/
[autovalue]: http://ryanharter.com/blog/2016/03/22/autovalue/