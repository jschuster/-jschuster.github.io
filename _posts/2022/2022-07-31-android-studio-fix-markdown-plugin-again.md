---
title: "Fix Markdown Plugin in Android Studio Chipmunk 2021.2.1" 
categories:
  - Development
tags:
  - android-studio
  - terminal
---

The good old [Markdown Plugin](https://plugins.jetbrains.com/plugin/7793-markdown) stopped working in Android Studio Chipmunk again.

For more details on why, see my old post [Fix Markdown Plugin in Android Studio Arctic Fox 2020.3.1 ]({{ site.baseurl }}{% link _posts/2021/2021-09-29-android-studio-fix-markdown-plugin.md%}). In short, the embedded JBR does not integrate JCEF ([Java Chromium Embedded Framework](https://plugins.jetbrains.com/docs/intellij/jcef.html)).

But the good news is that Chipmunk has made it even easier to change the JBR.

## Change JBR in Android Studio to fix Markdown plugin

So here are the steps to change the JBR in Android Studio Chipmunk:

- open menu **Help** > **Find Action...** (<kbd>⇧ Shift</kbd> + <kbd>⌘ Cmd</kbd> + <kbd>A</kbd>) and search for "Choose Boot Java Runtime for the IDE..." and execute
- In the dialog open the drop down **Select runtime**
  ![2022-07-31-android-studio-choose-jbr-dialog](../../assets/images/2022/2022-07-31-android-studio-choose-jbr-dialog.png)
- Take the default selection or choose from the **More Runtimes** one with JCEF in its title. Confirm with OK.
  ![2022-07-31-android-studio-choose-jbr-dialog-selection](../../assets/images/2022/2022-07-31-android-studio-choose-jbr-dialog-selection.png)
- After download and install you have to apply the changes with **Restart now**
  ![2022-07-31-android-studio-choose-jbr-confirm-dialog](../../assets/images/2022/2022-07-31-android-studio-choose-jbr-confirm-dialog.png)

Done. Now you can enjoy the full Markdown experience again.