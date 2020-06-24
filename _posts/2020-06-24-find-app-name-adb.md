---
layout: post
title:  "How to find an app name using package name through ADB Android?"
date:   2020-06-24 16:41:11 +0530
categories: android
---

## Pre-requisites

- Install ADB
- Connect your device and ensure `adb devices` shows up your phone

## Finding an application label using package name

### Setting up `aapt` binary on your Android

1. Find your CPU Architecture by `adb shell getprop ro.product.cpu.abi`
2. Depending on whether it's ARM-based or x86-based, download the corresponding `aapt` binary from here:  
[https://github.com/Calsign/APDE/tree/master/APDE/src/main/assets/aapt-binaries](https://github.com/Calsign/APDE/tree/master/APDE/src/main/assets/aapt-binaries)
3. Assume you have downloaded `aapt-arm-pie`.  
Now push it to your device by:
```
adb push aapt-arm-pie /data/local/tmp
adb shell chmod 0755 /data/local/tmp/aapt-arm-pie
```

### Getting App Label using package name

Assume you have the package name of Facebook App, which is `com.facebook.katana`

1. Find the APK path by:
```bash
$ adb shell pm list packages -f com.facebook.katana
```
Output:
```
package:/data/app/com.facebook.katana-GMPd_3qgg3cd8W8EqgXTtA==/base.apk=com.facebook.katana
```

2. Find the Application label of the APK by:
```bash
adb shell "/data/local/tmp/aapt-arm-pie dump badging /data/app/com.facebook.katana-GMPd_3qgg3cd8W8EqgXTtA==/base.apk | grep application-label"
```

Output:
```
application-label:'Facebook'
```

## Credits

- [Obtain common name using app package name - Android ADB](https://android.stackexchange.com/a/188229/112458)
