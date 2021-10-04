---
title: "Android (Studio) and the terminal" 
toc: true
categories:
  - Development
tags:
  - android
  - terminal
---

Sooner or later you will get to the point where you need to use Android tools via the terminal.

Tools like `adb` the [Android Debug Bridge](https://developer.android.com/studio/command-line/adb) to communicate with a running emulator or device. Or `emulator` to [start an emulator from the command line](https://developer.android.com/studio/run/emulator-commandline).

The tools are located deep in the paths of the Android SDK like `/Users/joachim/Library/Android/sdk/platform-tools/adb`. So that you don't always have to prepend the full path name, it's a good idea to extend the PATH variable.

Add the following lines to your `~/.zshrc`.

```bash
export ANDROID_SDK_ROOT=/Users/$USER/Library/Android/sdk
export PATH=${PATH}:$ANDROID_SDK_ROOT/emulator
export PATH=${PATH}:$ANDROID_SDK_ROOT/tools
export PATH=${PATH}:$ANDROID_SDK_ROOT/platform-tools
```

{: .notice--warning}

**Note:** Most likely you will find some tutorials and documents that mention that the environment variable `ANDROID_HOME` must be set. This is deprecated according to the [documentation](https://developer.android.com/studio/command-line/variables#envar). `ANDROID_SDK_ROOT` has exaclty the same effect and replaces  `ANDROID_HOME`.

{: .notice--warning}

**Note2:** Make sure to keep the order of the `PATH` export lines. The tool `emulator` is packed in `platform-tools` and `emulator` folder. The correct `emulator` is in the `emulator` folder. Keeping that before `platform-tools` makes sure that it will be the preferred one.

Restart your terminal program or run the following line to reload the zshrc configurations in the current terminal session:

```
source ~/.zshrc
```

## Terminal in Android Studio

Android Studio uses the configuration `~/.zshrc` as well. So with the exports above you will now be able to use the tools from the terminal within Android Studio as well.

### Shared History

Beginning with Android Studio Arctic Fox the embedded terminal also shares the terminal history `.zsh_history` with the system terminal. So no further configuration is required.

In Android Studio **4.1** `echo $HISTFILE` would result in something like 

```bash
/Users/joachim/Applications/Android Studio.app/Contents/plugins/terminal/.zsh_history
```

As mentioned, if you use Arctic Fox or newer you will see the default

```bash
/Users/joachim/.zsh_history
```

But to be on the safe side, I suggest that you add the following line to your `~/.zshrc`:

```bash
export HISTFILE=$HOME/.zsh_history
```

With this explicit setting, all terminals that share the `~/.zshrc` configuration share the same `.zsh_history` - even if a future Android Studio version has a regression - who knows ;)

<div class="notice--info" markdown="1">
**Tip**: If you use the terminal frequently, you may not want certain commands in the history. For example, you only want to mess around with some parameters and don't want to find every intermediate command in the history. Add the following line below the export HISTFILE line:

```bash
setopt HIST_IGNORE_SPACE
```

With this option, any command, with a preceding space, will NOT be included in the history.

For more inspiration, check out my [zshrc Github repo](https://github.com/jschuster/zshrc).
</div>
