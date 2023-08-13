---
title: "Gradle 8 and JDK 17 upgrade - follow-ups" 
toc: true
categories:
  - Development
tags:
  - gradle
  - java
  - android
---

A couple of questions came up during the [transition to Gradle 8 and thus the transition to JDK 17]({{ site.baseurl }}{% link _posts/2023/2023-08-12-gradle-and-jdks.md %}).

1. What is the difference between setting target versions and using toolchain?
2. Recommended approach now in 2023?
3. can this work well with Java 17 on e.g. Android 7.0 (aka 24) devices (definitely don't have a Java 17 compatible DVM yet)

In this post I would like to clarify the first two questions directly (even if it must somehow fulfill the 3rd question, but more on that later).

# The thing with the target version briefly explained

The Java or Kotlin compiler translates the sources into class files. These class files receive a version, which indicates to the executing JVM, which version it must have at least, in order to be able to execute the bytecode in it.

With `targetCompatibility` or `jvmTarget` one determines the version, which are written into the Class files. So you can set a class file, which was created by Java compiler of the version 17 consciously nevertheless e.g. the target version 8.

What for? You might want to make sure that the compiled code runs reliably on an old JVM 8 even though you want or have to use the more recent JDK 17 compiler. 

To make sure that you don't accidentally use Java 11 or 17 language resources in your source code, there is another parameter [`sourceCompatibility`](https://developer.android.com/build/jdks#source-compat). This parameter makes sure that the IDE linter will immediately react if you accidentally use a language feature of a newer Java version.

{: .notice--info}

**Info:** Specifying `targetCompatibility` does not do any conversion or similar magic. So you cannot simply use the latest Java 17 language tools and expect that by this setting then the compiler will generate from it bytecode compatible for e.g. JVM 8.<br>This also explains why `sourceCompatibility` **must not be higher** than `targetCompatibility`. It would calm down the linter and thus one could diligently use Java 17 language means in the code. When compiling with a Java 17 compiler, a valid class file would also still come out. But by setting the major version in the class file (via targetCompatibility) to Java 8, a JVM 8 would accept that but then fail on the Java 17 bytecode at runtime.

# The new recommended way - toolchain

[Gradle 6.7 itroduced the "Java toolchain support"](https://blog.gradle.org/java-toolchains) and has become so established that the [Google doc also makes the clear recommendation](https://developer.android.com/build/jdks#toolchain) to rely on the toolchain specification. [The error output mentioned in the previous post]({{ site.baseurl }}{% link _posts/2023/2023-08-12-gradle-and-jdks.md %}) also suggests this solution.

By specifying the toolchain, you tell Gradle which JDK and thus which compiler version you specifically want to use to compile the sources. It does not simply set the `targetCompatibility` in the class files, as described above. Gradle 8 can thus use any other JDK to compile the sources, whether that is the same JDK 17 or a completely different one.

The advantage:

1. you can specify a specific version regardless of the JDK running Gradle, which will then be reliably maintained
   
2. since the setting of the toolchain then also set `targetCompatibilty`, `sourceCompatibilty` and `jvmTarget`, you can save these specifications
   
3. if one uses Kotlin (possibly also gladly mixed with Java), only the specification for the Kotlin compiler is sufficient

So instead of setting 3 configurations via

```groovy
compileOptions {
   sourceCompatibility JavaVersion.VERSION_17  
   targetCompatibility JavaVersion.VERSION_17  
}

kotlinOptions {  
   jvmTarget = '17'
}
```

you can replace it with just this one

```groovy
kotlin {  
    jvmToolchain 17  
}
```

<div class="notice--warning" markdown="1">

**Note:** If you use something else for the toolchain than the JDK that Gradle runs, you have to consider the following:<br>

1. make sure that this JDK is actually installed (possibly the only option for build servers whose setup cannot be changed dynamically). Gradle tries to determine [the best possible JDK version](https://docs.gradle.org/current/userguide/toolchains.html#sec:precedence).
2. use the so-called [Toolchein Resolver Pluign](https://docs.gradle.org/current/userguide/toolchains.html#sub:download_repositories) - with it Grade downloads if necessary a JDK, if it does not find a suitable installed one.

</div>

# Conclusion and recommendation

So the difference is with setting the target version, you really only set the target version in the class files. It is possible to use a different compiler intentionally or unintentionally. With toolchain you get what you specify.

My personal recommendations:
1. if you have upgraded a project to the latest Gradle 8.2.1 + AGP 8.1, just set the `jvmToolchain` for Kotlin only

2. in addition you are free to also mix that with setting `sourceCompatibility`, `targetCompatibility`, or `jvmTarget`

   - either if you are still dealing with older AGP versions,  see warning in [https://kotlinlang.org/docs/gradle-configure-project.html#gradle-java-toolchains-support](https://kotlinlang.org/docs/gradle-configure-project.html#gradle-java-toolchains-support)

   - or because you just want to force the linter to warn you on using new language features

3. you can set the toolchain to JDK 17 - so you will avoid differences in JDK running Gradle and JDK compiling the code



{: .notice--warning}

**Note:** Not specifying anything at all is a bad choice, by the way: If you omit all specifications, Java version 8 will be assumed by default - not the compiler's version, as you might think. And since this can also lead to unexpected behavior, it is advisable to specify something in any case.

