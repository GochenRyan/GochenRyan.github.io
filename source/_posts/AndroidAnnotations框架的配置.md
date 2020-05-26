---
title: AndroidAnnotations框架的配置
id: 5
date: 2018-05-23 21:09:45
tags:
    - Android
---

网上教程若干，不适用于最新的Android Studio版本。
故根据官网教程配置。
[官网教程](https://github.com/androidannotations/androidannotations/wiki/Building-Project-Gradle)

<!-- more -->

## 对project的build.gradle进行配置
在**repositories**下添加 ：
```
repositories {
        ...
        mavenCentral() 
    }
```
在**allprojects**添加：
```
allprojects {
    repositories {
        ...
        maven {
            url = 'https://oss.sonatype.org/content/repositories/snapshots'
        }
    }
}
```


## 对moduler的build.gradle进行配置
需要注意的一点是：官网配置采用的是compile。
但是，**compile方法过时了，所以采用api替代compile**。
在**dependencies**下添加：
```
def AAVersion = '4.4.0'    //由于项目采用之前的版本有问题，故采用最新版本4.4.0
def AASnapshotVersion = '4.4.0-SNAPSHOT'
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.1.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'

    annotationProcessor "org.androidannotations:androidannotations:$AASnapshotVersion"
    api "org.androidannotations:androidannotations-api:$AASnapshotVersion"
}
```