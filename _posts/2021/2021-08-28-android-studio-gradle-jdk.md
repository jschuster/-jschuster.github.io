---
title: "Android Studio manage Gradle JDKs"
categories:
  - Development
tags:
  - android
  - gradle
  - java
---

Updating Android Studio is always some 'exciting' event - what changes and will it break my current setup.
Some changes in 4.2 made me imediatelly rollback to 4.1.3.
Android Studio 2020.3.1 Arctic Fox got a new chance and I'm really plessantly surprised!

A [small feature already introduced in 4.2](https://developer.android.com/studio/releases#4.2-bundled-jdk-11) is the ability to manage installed JDKs from Android Studio.
Till 4.2 you might not bother setting some JDK / JAVA_HOME stuff at all - Android Studio used the bundled JDK 8.

Having to deal with e.g. custom cacerts in your JDK security folder - you might got a [little frustrated](https://stackoverflow.com/questions/51373608/some-conflicts-were-found-in-the-installation-area-android-studio-cacerts-er) about the Android Studio updating process.

Handling your JDK differently was already possible but a little knowledge, tools (like [SDKMAN!](https://sdkman.io/jdks)) or [tutorials](https://mkyong.com/java/how-to-install-java-on-mac-osx) are necessary.

## Integrated Preference for Gradle JDK management

But now there is a nice JDK configuration integrated in the Android Studio preferences.

Open `Preferences...`, navigate to `Build, Execution, Deployment` > `Build Tools` > `Gradle`

In the menu `Grade JDK:` you can see the currently selected JDK for the current project. From this menu you can select the other JDKs found. Or you can Download a new one with `Download JDK...`.

![Android Studio Preferences Gradle JDK](../../assets/images/2021/2021-08-28-android_studio_preferences_gradle_jdk.png)



Form the dialog you can select your `Version` and your `Vendor` and the `Location` to install the JDK to.

![2021-08-28-android_studio_preferences_gradle_jdk_download](../../assets/images/2021/2021-08-28-android_studio_preferences_gradle_jdk_download.png)

![2021-08-28-android_studio_preferences_gradle_jdk_download](../../assets/images/2021/2021-08-28-android_studio_preferences_gradle_jdk_download_vendor.png)

With the separate Installation of the JDK customizations in that instance will not affect your Android Studio bundled JDK.

Another benefit is that Android Studio will notify you on updates to the installed JDK.

![2021-08-28-android_studio_notification_jdk_update](../../assets/images/2021/2021-08-28-android_studio_notification_jdk_update.png)
