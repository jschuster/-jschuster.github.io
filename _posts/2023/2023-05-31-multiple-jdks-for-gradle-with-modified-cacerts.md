---
title: "Use multiple JDKs for Gradle with common modified cacerts" 
categories:
  - Development
tags:
  - gradle
  - java

---

You already use your project with [another JDK than the one embedded by Android Studio]({{ site.baseurl }}{% link _posts/2021/2021-08-28-android-studio-gradle-jdk.md%}) and have to have some JDK versions in parallel. Thats no big deal.

Trouble starts if you have to use custom certificates to communicate with your own server for e.g. downloading dependencies.

You have to patch those custom certificates to the JDKs `cacerts`. Every JDK installation brings its own `cacerts`. So you have to do that patching every time you install a new JDK or update an existing. Even worse if you use the Android Studio embedded JDK - if you patch that `cacerts`,  your next update to Android Studio will fail.

However, if you have failed to patch the `cacerts`, you will get an error when you build your project with the new JDK and try to download dependencies from your server that require a custom certificate.

So to avoid having to patch every `cacerts` for every JDK version, it would be nice to have a common custom `cacerts` and use it regardless of the JDK version used.

You can achieve this by running `gradle` with the `javax.net.ssl.trustStore` parameter as follows:
```
gradle build -Djavax.net.ssl.trustStore=/PATH/TO/custom_cacerts
```

{: .notice--warning}

**Note:** The path to your custom_cacerts must be an absolute path. For example, you cannot use ~/custom_cacerts or $HOME/custom_cacerts.

Even better, you can set this parameter as the default for each Gradle execution by specifying it in your `gradle.properties` file. This way you don't have to pass it along with the `gradle` command every time, and Android Studio uses this parameter as default as well.

Add the following line to the `gradle.properties`  in your [gradle user home](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home) `~/.gradle/gradle.properties`:
```groovy
systemProp.javax.net.ssl.trustStore=/PATH/TO/custom_cacerts
```

{: .notice--info}

**Info:** You can configure Gradles  Java process by providing parameters in the `gradle.properties`. The `gradle.properties` in your [gradle user home](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home) can be used for global settings that will be used in every project. It will override those settings you may share with a `gradle.properties` file in your project folder.<br><br>For more details, see the [Gradle properties documentation](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties).
