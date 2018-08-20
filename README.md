# Riru

Riru is a very simple thing. It provides ability to modules to run their code in apps' or system server's process.

## Requirements

* Rooted Android 6.0+ devices 
* Magisk (use to replace system file, temporarily only provide Magisk zip)

## How it work?

In short, replace a shared library which will loaded by zygote process.

First we need to find that library. The library needs to be as simple as possible, so we found libmemtrack, with only 10 exported functions. Then we can provide a library named libmemtrack with all its functions, so the functionality will not be affected and we will able to in the zygote process.

Now the next question, how to know if we are in a app process or a system server process. We found some JNI functions (`com.android.internal.os.Zygote#nativeForkAndSpecialize` & `com.android.internal.os.Zygote#nativeForkSystemServer`) will be called when a app or system server is forked. So we just need to replace these functions to ours. This part is violent, just search method name in memory. At the end, `JNINativeMethod` of these methods can be found, then we can call RegisterNatives again to replace them.

## Why Riru is made?

There is only one `libmemtrack.so`, if someone wants do something by replace it, others can't. So I made Riru to occupy libmemtrack, but provide ability to make modules.

## Build Requirements

1. Android NDK (add directory with `ndk-build` to `PATH`)
2. `zip` to create zip file (add to `PATH`)
3. (Windows only) Envirment to run `build.sh` script

## Build core

Run `gradlew :riru-core:zip` in command line or Android Studio, zip will be saved to `release`

## Build your own module

1. Copy `riru-module-template` and rename to your name
2. Change module name in `riru-your-module/build.sh` `riru-your-module/template_override/module.prop` `riru-your-module/template_override/riru_module.prop` `riru-your-module/jni/main/Android.mk`
3. Write your code
4. Run `:riru-your-module:zip` task in command line or Android Studio, zip will be saved to `release`

## Install

Current only support Magisk.

1. Install core zip in Magisk
2. Install module zip in Magisk