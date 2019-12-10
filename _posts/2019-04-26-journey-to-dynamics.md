---
layout: post
title: Dynamic features in an existent app
description: My journey implementing a dynamic feature in an existent app
tags: 
    - Kotlin
    - Dynamic Features
    - App Bundle
    - Android
---

```xml
    <dist:module
        dist:title="HolaMundo"
        dist:onDemand="false">
    </dist:module>
```TODO string

```xml
    <application>
        <activity android:name=".KycFormActivity">
            <intent-filter>
            <action android:name="android.intent.action.VIEW"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <category android:name="android.intent.category.BROWSABLE"/>
            <data android:scheme="http"/>
            <data android:host="testing"/>
            </intent-filter>
        </activity>
    </application>

````
The application used is the one defined in app module

With proguard activated we loss the build speed advantage