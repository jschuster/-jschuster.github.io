---
title: "Launch emulator with parameters from Android Studio on a Mac" 
toc: true
categories:
  - Development
tags:
  - macos
  - android-studio
  - emulator
---

Starting an emulator from Android Studio is quite a normal thing. But if you want to provide some parameters along with the emulator startup, you will encounter some problems.

The Android Studio documentation has a big chapter about the emulator and also an extra page for [emulator command line parameters](https://developer.android.com/studio/run/emulator-commandline).

There you will find a lot of useful parameters documented. However, the problem is that you have to start the emulator from the terminal to specify the parameters along with the command line. Android Studio does not provide a way to pass some parameters, and it is cumbersome to say the least to always start the emulator from the terminal and then use it from Android Studio.

{: .notice--info}
**Note:** In Arctic Fox and before, the Android Studio emulator was launched as a separate application. It respected at least some of the parameters like `export QEMU_AUDIO_DRV=none` that were set in the `.zshrc`. With Bumblebee, the parameters set in the `.zshrc` do not work at all when you use the emulator embedded in Android Studio.

But since 2018 there is a [hidden feature](https://github.com/JetBrains/android/commit/dd839c9436e425381256cb2e984305c09a7fbbd9)  to set custom parameters for emulators in Android Studio. So it is quite easy to set an environment variable `studio.emu.params` to provide parameters to the emulator on startup via Android Studio - at least for Windows and Linux users - most Mac users will have a hard time setting an environment variable with dots in the name via the often mentioned `.zshrc`.

{:.notice--warning}
**Note:**  You will find some tutorials suggesting to set the environment variables via `.zshrc`. But this works only for applications that are somehow started from the command line to take the settings in `.zshrc`. And there is one more limitation with this environment variable - you cannot set environment variables with dots in their names in the shell: `export studio.emu.params=-writable-system` will not work.

## Set the studio.emu.params
macOS has no GUI to set the environment variables for applications like Windows does. `launchctl` is the tool to set environment variables for macOS applications.

With the following command you will set the environment variable
```
launchctl setenv studio.emu.params -writable-system
```

And with the following command, you can read the current value
```
launchctl getenv studio.emu.params
```

Android Studio reads this parameter at startup, so changes will only take effect after Android Studio is (re)started.

This setting will remain until the next time you restart your Mac, so it is very useful for evaluating some changes.

To make things more permanent you need to create a `.plist` file e.g. `studio-environments.plist` in the folder `~/Library/LaunchAgents/` with the following content:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>setenv.studio.emu.params</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/launchctl</string>
        <string>setenv</string>
        <string>studio.emu.params</string>
        <string>-writable-system</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

To load this changes directly (e.g. to apply changes without rebooting your Mac)
```
launchctl load ~/Library/LaunchAgents/studio-environments.plist
```

If you run the command while it is already loaded, you will get an error message like this
```
Load failed: 5: Input/output error
```

You have to unload it before with

```
launchctl unload ~/Library/LaunchAgents/studio-environments.plist
```

You can check your changes with `launchctl getenv studio.emu.params`, as described above.

<div class="notice--info" markdown="1">
**Note:** You can set multiple parameters. The parameters must be separated with a comma `,` - no space.

```
launchctl setenv studio.emu.params -writable-system,-noaudio
```

In the XML you have to set it in one line
```
<string>-writable-system,-noaudio</string>
```

</div>

{: .notice--primary}
**Tip:** If you have some Bluetooth headphones and notice strange hissing sounds while the emulator is running, you might find it useful to add the `-noaudio` mentioned above. You won't hear any sound from the emulator anymore, but the hissing will be gone too ;)

