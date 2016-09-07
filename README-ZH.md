[🇨🇳 中文](README-ZH.md) / [🇯🇵日本語](README-JP.md) / [🇬🇧 English](README.md)
# 🍼Debug Bottle
Android Java / Kotlin 程序员开发调试工具。Debug Bottle的所有功能均建立在App的debug版本中，不会对release版本产生任何影响。Debug Bottle旨在提高开发效率，把控App质量。

[<img src="screenshots/main-panel.png"/>](screenshots/raw/main-panel.png)
[<img src="screenshots/features.png"/>](screenshots/raw/features.png)
[<img src="screenshots/features-2.png"/>](screenshots/raw/features-2.png)

## 功能

- [简易的HTTP请求嗅探](#Http监听)
- [3D化Activity视图](#3D化视图)
- [Shared Preferences编辑器](#shared-preferences编辑器)
- [使用Strict mode调试软件](#使用strict-mode)
- [轻松找到可能出现的内存泄漏代码](#使用leak-canary)
- [找到可能出现的UI卡顿代码](#使用block-canary)
- [简易创造Activity入口，及测试Runnable](#进入一切activity并注入参数)

#### Http监听
通过OkHttp的拦截器监听通过App的Http请求，并记录在Debug Bottle的日志中。记录过的日志可以通过分享按钮分享给其他开发者。

[<img src="screenshots/network-sniffer-1.png"/>](screenshots/raw/network-sniffer-1.png)
[<img src="screenshots/network-sniffer-2.png"/>](screenshots/raw/network-sniffer-2.png)

#### 3D化视图
通过设置打开“3D”功能，可以更直观地看到当前Activity的layout结构。
本功能支持单点手势旋转，双点手势缩放和位移。

[<img src="screenshots/scalpel-view.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/scalpel-view.gif" width="225" height="400" />](screenshots/raw/scalpel-view.gif)

#### Shared Preferences编辑器
通过Debug Bottle可以简单地编辑App用到的所有Shared Preferences。

[<img src="screenshots/shared-preferences-editor-1.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/shared-preferences-editor-2.png"/>](screenshots/raw/network-sniffer-2.png)

#### 使用Strict Mode
Debug Bottle可以在运行时启用或禁用Strict Mode。StrictMode最常用来捕捉应用程序的主线程，它将报告与线程及虚拟机相关的策略违例。一旦检测到策略违例（policy violation），你将获得警告，其包含了一个栈trace显示你的应用在何处发生违例。除了主线程，我们还可以在Handler，AsyncTask，AsyncQueryHandler，IntentService等API中使用StrictMode。更多使用方法介绍，参见[官方开发者文档](https://developer.android.com/reference/android/os/StrictMode.html)。

#### 使用Leak Canary
Debug Bottle中编译进了Leak Canary，所有Leak Canary的功能都可以使用了，并且可以通过Debug Bottle来控制开关。Leak Canary的使用方法请参考[官方Wiki](https://github.com/square/leakcanary/wiki/FAQ)。

#### 使用Block Canary
加入了支持Kotlin的Block Canary，可以监控UI线程卡死。

[<img src="screenshots/ui-blocks.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/block-canary-demo.gif" width="225" height="400" />](screenshots/raw/block-canary-demo.gif)

#### 进入一切Activity并注入参数
简单的Activity／Runnable入口。可以随心所欲进入任何一个Activity，使用任何参数，甚至可以向App中注入想要测试的Intent。

[<img src="screenshots/all-activities.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/run-activity-with-intent.gif" width="225" height="400" />](screenshots/raw/run-activity-with-intent.gif)

## 使用方法

安装嵌入Debug Bottle的App后，你将看到初你App外的新图标🍼。点击进入Debug面板。

## 搭建方法

#### 1. 加入Gradle依赖
首先在工程Gradle文件中添加snapshot源：
```gradle
allprojects {
    repositories {
        ...
        maven {
            url "https://oss.sonatype.org/content/repositories/snapshots"
        }
    }
}
```
然后在主模块中（Application类所在模块）加入依赖：

```gradle
dependencies {
    debugCompile 'com.exyui.android:debug-bottle-runtime:1.0.0EAP-SNAPSHOT'

    // 如果你的工程是Java工程,使用此依赖
    releaseCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0EAP-SNAPSHOT'

    // 如果你的工程是Kotlin工程,使用此依赖
    releaseCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0EAP-SNAPSHOT'

    compile 'com.android.support:appcompat-v7:23.2.0+'
    compile 'com.android.support:support-v4:23.2.0+'
}
```

#### 2. 编辑Manifest
加入Debug Bottle的主Activity：
```xml
<activity
    android:name="com.exyui.android.debugbottle.components.__DTDrawerActivity"
    android:theme="@style/Theme.AppCompat.Light"/>
```

#### 3. 在Application中插入Debug Bottle代码

首先实现Block Canary运行必要的上下文类：
```java
public class AppBlockCanaryContext extends BlockCanaryContext {...}
```

然后在Application的onCreate中插入代码：
```java
public class MyApplication extends Application{
    @Override
    public void onCreate() {
        super.onCreate();
        Installer.INSTANCE.install(this)
            .setBlockCanary(AppBlockCanaryContext(this))
            .setOkHttpClient(httpClient)
            .setInjector("your.package.injector.ContentInjector")
            .setPackageName("your.package")
            .enable()
            .run();
    }
}
```

或者如果你的工程是Kotlin,你还可以这样写:
```kotlin
class MyApplication: Application() {
    override fun onCreate() {
        super.onCreate()
        Installer.install(this)
            .setBlockCanary(AppBlockCanaryContext(this))
            .setOkHttpClient(httpClient)
            .setInjector("your.package.injector.ContentInjector")
            .setPackageName("me.chunyu")
            .enable()
            .run()
}
```

收工。

## 相关链接
* [Leak Canary](https://github.com/square/leakcanary)
* [Android Performance Monitor](https://github.com/markzhai/AndroidPerformanceMonitor)
* [Scalpel](https://github.com/JakeWharton/scalpel)

## License

```
Debug Bottle

Copyright 2016 Yuriel (http://exyui.com).

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

Android Performance Monitor

Copyright (C) 2016 MarkZhai (http://zhaiyifan.cn).

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

Leak Canary

Copyright 2015 Square, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

Scalpel

Copyright 2014 Jake Wharton

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
