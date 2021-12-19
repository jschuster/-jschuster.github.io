---
title: "Manage multiple Java JDK versions on a Mac" 
toc: true
categories:
  - Development
tags:
  - java
  - macos
  - terminal
---

As an Android developer, you will most likely have come into contact with Java and its JDK (Java Development Kit). Gradle version 7 and above requires JDK version 11 - which is bundled with Android Studio 2020.3.1 Arctic Fox.

If you have read my post about [installing and managing updates of your JDK versions with Android Studio]({{ site.baseurl }}{% link _posts/2021/2021-08-28-android-studio-gradle-jdk.md %}) you have found an option to install and update the JDK(s) separate from the Android Studio embedded one. From time to time you may need a specific version for different projects.


Running `java` from the terminal runs by default the latest installed version. So starting e.g. `./gradlew app:assembleRelease` from the terminal will use the Java JDK that is currently set.


## Set specific JDK version

{: .notice--info}

**Note:** `/usr/bin/java` is not just a symbolic link to a Virtual Machines `/bin/java` binary. Instead, it behaves like a proxy binary by default - it finds the latest installed JDK and uses that when executing. However, you can instruct it to use a specific version.

If you want to run a specific version you have to specify the environment variable `$JAVA_HOME` pointing to the path of the version you want to use.

You can list all installed Java versions with the following command:

```bash
/usr/libexec/java_home -V
```

Will show you a list like this

```bash
Matching Java Virtual Machines (4):
    11.0.12.1 (x86_64) "Amazon.com Inc." - "Amazon Corretto 11" /Users/joachim/Library/Java/JavaVirtualMachines/corretto-11.0.12/Contents/Home
    1.8.0_312 (x86_64) "Amazon" - "Amazon Corretto 8" /Users/joachim/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home
    1.8.0_302 (x86_64) "Amazon" - "Amazon Corretto 8" /Users/joachim/Library/Java/JavaVirtualMachines/corretto-1.8.0_302/Contents/Home
    1.8.0_275 (x86_64) "Amazon" - "Amazon Corretto 8" /Users/joachim/Library/Java/JavaVirtualMachines/corretto-1.8.0_275/Contents/Home
/Users/joachim/Library/Java/JavaVirtualMachines/corretto-11.0.12/Contents/Home
```

The last line shows the path to the latest Java home.

You can get the path to a specific version with the following command:

```bash
/usr/libexec/java_home -v1.8
```

This will get the path to the latest installation of the specified version 1.8

```
/Users/joachim/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home
```

Combining this you can modify your `.zshrc` (resp. `.bashrc`) to set your `$JAVA_HOME` to a specific version

```bash
# Path to JAVA
export JAVA_HOME=$(/usr/libexec/java_home -v1.8)
```

Starting a terminal now you will see the output of `echo $JAVA_HOME` resulting in the path

```
/Users/joachim/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home
```

And running `java -version` will show you the version details like this

```bash
openjdk version "1.8.0_312"
OpenJDK Runtime Environment Corretto-8.312.07.1 (build 1.8.0_312-b07)
OpenJDK 64-Bit Server VM Corretto-8.312.07.1 (build 25.312-b07, mixed mode)
```

## Change JDK version (temporarily)

You might like to change the version just for your current terminal session a little more convenient with the following function added to your `.zshrc`

```bash
jdk() {
      version=$1
      unset JAVA_HOME;
      export JAVA_HOME=$(/usr/libexec/java_home -v"$version");
      java -version
}
```

In a new terminal session you will be able to use that function like this

```bash
jdk 11
```

This sets `$JAVA_HOME` to the latest 11 version path and so `java -version` will result in 

```bash
openjdk version "11.0.12" 2021-07-20 LTS
OpenJDK Runtime Environment Corretto-11.0.12.7.2 (build 11.0.12+7-LTS)
OpenJDK 64-Bit Server VM Corretto-11.0.12.7.2 (build 11.0.12+7-LTS, mixed mode)
```

Setting the version with the fuction `jdk` will change the version for this session - closing this terminal session and starting a new one will take the value of the `$JAVA_HOME` set with first `export`.
