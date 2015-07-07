---
title: Run custom shell scripts in Android
author: James Tang
layout: post
permalink: /notes/run-custom-shell-scripts-in-android/
categories:
  - Notes
tags:
  - ADB
  - Android
  - Shell
---
It&#8217;s not secure and inconvenient to do some low-level testing task depending on third party APPs. Shell is especially useful for such purpose. 

## 1. Install Android Debug Bridge([ADB][1])

Download Android SDK and run SDK Manager to install the platform-tools, as following image shows:

[<img src="http://tangobean.com/wp-content/uploads/2013/03/android-adb-500x357.jpg" alt="android-adb" width="500" height="357" class="alignnone size-medium wp-image-746" />][2]

## 2. Connect to android device with ADB

In order to push file into /system file system, root mount is required, so run following commands:

<pre class="brush:bash">$ adb root
$ adb remount
</pre>

If no error occurred, you should see &#8220;remount succeeded&#8221; on terminal.

## 3. Write shell scripts

It&#8217;s almost the same as writing general shell scripts for Linux except the first line, for the android shell is located at /system/bin directory.

<pre class="brush: bash">#!/system/bin/sh

# DO SOMETHING
</pre>

## 4. Push script to android

Push the script, test_script for example, to android:

<pre class="brush:bash">$ adb push /path/to/test_script /system/xbin/test_script
</pre>

And change permission:

<pre class="brush:bash">$ adb shell chmod 6755 /system/xbin/test_scripts
</pre>

## 5. Run from Android

If you wanna run the scripts on Android, you&#8217;d better install [Android Terminal Emulator][3].

 [1]: http://developer.android.com/tools/help/adb.html
 [2]: http://tangobean.com/wp-content/uploads/2013/03/android-adb.jpg
 [3]: https://play.google.com/store/apps/details?id=jackpal.androidterm&hl=zh_CN