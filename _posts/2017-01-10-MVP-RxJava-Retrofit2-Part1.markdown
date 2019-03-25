---
title: "MVP with Retrofit2 and RxJava2 - 2"
last_modified_at: 2017-01-10T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Gradle and ProGuard setup"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

The first step after creating a project is to properly configure the *build.gradle* and *proguard-rules.pro* files

# Gradle
We like magic right? who doesn't!

{: style="text-align:center"}
![Magic!][magicImage]

While programming though magic (numbers) are nice when grouped in classes or files all together instead of scattered all around the code base. This applies also to the ones used in the gradle files, that is why in the **Project** *build.gradle* we can write the one we'll need all together

{% highlight ruby %}
ext {
    androidCompileSdkVersion = 25
    androidBuildToolsVersion = '25.0.1'
    androidMinSdkVersion = 16
    androidTargetSdkVersion = 25
}
{% endhighlight %}

Furthermore, to have files clean and to the point, we can also create a *dependencies.gradle* file containing the libraries used in the project

{% highlight ruby %}
ext {
    supportV = '25.1.0'
    rxV = '1.1.0'

    dependencies = [
      appCompat: "com.android.support:appcompat-v7:$supportV",
      design:    "com.android.support:design:$supportV",
      rxJava:    "io.reactivex:rxjava:$rxV",
      rxAndroid: "io.reactivex:rxandroid:$rxV",
      ...
    ]
}
{% endhighlight %}

Then we can refer to the file from the **Project** *build.gradle* with the line

{% highlight ruby %}
apply from: 'dependencies.gradle'
{% endhighlight %}

These two tricks combines make then possible to write a **Module** *build.gradle* that looks like this

{% highlight ruby %}
compileSdkVersion rootProject.ext.androidCompileSdkVersion
buildToolsVersion rootProject.ext.androidBuildToolsVersion

defaultConfig {
    applicationId "com.kioli.mvpretrorxexample"
    minSdkVersion rootProject.ext.androidMinSdkVersion
    targetSdkVersion rootProject.ext.androidTargetSdkVersion
    ...
}
dependencies {
    Map<String, String> dependencies = rootProject.ext.dependencies;

    provided dependencies.autoValue
    ...
    annotationProcessor dependencies.butterknifeApt
    annotationProcessor dependencies.autoValue
    ...
    compile dependencies.appCompat
    compile dependencies.rxAndroid
    compile dependencies.rxJava
    ...
}
{% endhighlight %}

From android gradle plugin 2.2 the support for annotations is granted by the keyword *annotationProcessor*, whereas before we needed to use the [apt-plugin][apt-plugin] from Hugo Visser

&nbsp;

# Useful libraries

Of course writing a post about Retrofit and RxJava means dealing with these two main libraries, but there are some other nice libraries which could help us out, namely:

* **[Butterknife][lib-butter]**: Used to bind fields and methods to views

* **[Icepick][lib-ice]**: Used to automate saving and restoring instance state

* **[AutoValue][lib-auto]**: Used for value types classes to remove boilerplate code

&nbsp;

# ProGuard

ProGuard, we know, is used to obfuscate our code to prevent (or delay) reverse engineering of the app, and is enabled enabling **_useProguard_** inside the **Module** *build.gradle*

{% highlight ruby %}
buildTypes {
    release {
        minifyEnabled true
        useProguard true
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
{% endhighlight %}

Enabling **_minifyEnabled_** instead shrinks the total size of the app pruning unused files and classes and compressing the heck out of them

Since some of the external libraries we use do not work well with complete brute-force obfuscation we need to write these lines in the *proguard-rules.pro* file otherwise the production version of our app will not build

{% highlight ruby %}
-dontwarn icepick.**
-keep class icepick.** { *; }
-keep class **$$Icepick { *; }
-keepclasseswithmembernames class * {
    @icepick.* <fields>;
}
-keepnames class * { @icepick.State *;}

-dontwarn okio.**
-dontwarn retrofit2.Platform$Java8
-dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement
-dontwarn sun.misc.**

-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
   long producerIndex;
   long consumerIndex;
}

-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode producerNode;
}

-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode consumerNode;
}
{% endhighlight %}

Next topic will be [core classes (MVP architecture)][next]

[next]: https://kioli.github.io/android/MVP-RxJava-Retrofit2-Part2/
[apt-plugin]: https://bitbucket.org/hvisser/android-apt
[lib-butter]: http://jakewharton.github.io/butterknife/
[lib-ice]: https://github.com/frankiesardo/icepick
[lib-auto]: https://github.com/google/auto/blob/master/value/userguide/index.md
[magicImage]: https://kioli.github.io/assets/images/post/001-mvp/magic.gif