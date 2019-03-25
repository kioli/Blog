---
title: "MVP with Retrofit2 and RxJava2 - 1"
last_modified_at: 2017-01-05T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Intro"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

I have stumbled upon many tutorials scraping the surface of technologies/libraries/patterns that help us having a cleaner and more robust way of programming, but they were not really hands-on for a real world production app

Therefore I decided to create a [simple app][app] that queries a web API (making two calls together) and shows the result on a list… pretty simple right? Well yes, but you need to do things right ;)

I am gonna touch base on different topics, some can be more trivial some may be more of interest, so instead of creating a bible-long post I decided to split it in parts

* [Creation of the project and Gradle/ProGuard setup][part1]

* [Core classes (MVP architecture)][part2]

* [Core classes (Butterknife and Icepick)][part3]

* [Retrofit2 configuration][part4]

* [Binding of the MVP][part5]

* [Creation of the main activity/view][part6]

Follow me if you want to see how deep the rabbit hole goes…

{: style="text-align:center"}
![blue or red pill?][pillsImage]

[app]: https://github.com/kioli/MvpRetroRxExample
[part1]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part1/
[part2]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part2/
[part3]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part3/
[part4]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part4/
[part5]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part5/
[part6]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part6/
[pillsImage]: https://kioli.github.io/assets/images/post/001-mvp/bluepill.jpg