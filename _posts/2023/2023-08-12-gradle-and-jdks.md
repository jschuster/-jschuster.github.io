---
title: "Gradle 8 and JDK 17 upgrade" 
categories:
  - Development
tags:
  - gradle
  - java
  - android
---

Now that Gradle 8 and with it AGP 8 have been out for quite a while, we can finally dare to update the project to Gradle 8 as well.

Gradle 8 needs Java 17, so the good old environment variable `JAVA_HOME` has to be [adapted accordingly]({{ site.baseurl }}{% link _posts/2021/2021-12-19-manage-multiple-java-jdk-versions.md %}).

Thanks to the AGP Upgrade Assistant, upgrading the settings is done with one click. 

To upgrade the wrapper as well it is a good idea to perform the [official migration](https://docs.gradle.org/current/userguide/upgrading_version_7.html) step as well.

```shell
./gradlew wrapper --gradle-version 8.2.1
```

Sync and Build ... and Fail.

```
- 'compileDebugJavalithJavac' task (current target is 1.8) and 'kaptGenerateStubsDebugKotlin' task (current target is 17) jvm target compatibility should be set to the same Java version.  
    Consider using JVM toolchain: https://kotl.in/gradle/jvm/toolchain
```

Okay, so what does that mean?

Since Gradle is now running JDK 17, all tools and compilers that Gradle launches will also run with the version from that JDK 17.

As the error message says, all targets must match. And it also gives a hint on how to achieve that.

So far, the project is configured the way Android Studio's sample projects are preconfigured even today.

```groovy
compileOptions {
   sourceCompatibility JavaVersion.VERSION_1_8  
   targetCompatibility JavaVersion.VERSION_1_8 
}

kotlinOptions {  
   jvmTarget = '1.8'
}
```

In the error message you can see that the Java compiler complies. However, it seems that kapt does not.

Without investigating further, why kapt does not consider the jvmTarget or targetCompatibility, it is easiest to simply adjust the corresponding values to Java version 17.

Now that we already have the entries for compileOptions and kotlinOptions and can adjust them accordingly, a first solution looks like this.

```groovy
compileOptions {
   sourceCompatibility JavaVersion.VERSION_17  
   targetCompatibility JavaVersion.VERSION_17  
}

kotlinOptions {  
   jvmTarget = '17'
}
```

After that, the build runs through again without further problems (ok, you may also need to update some dependencies like `kotlin-gradle-plugin`, but you would do this anyway 😉).
