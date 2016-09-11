[🇨🇳 中文](README-ZH.md) / [🇯🇵日本語](README-JP.md) / [🇬🇧 English](README.md)
# 🍼デバッグボットル
アンドロイド Java / Kotlin 開発者ツール

- [CHANGELOG](CHANGELOG.md)
- [TODO](TODO.md)

DEMOは今Google Playでダウンロードできます:

<a href="https://play.google.com/store/apps/details?id=me.chunyu.dev.yuriel.kotdebugtool"><img alt="Get it on Google Play" src="https://play.google.com/intl/en_us/badges/images/apps/en-play-badge-border.png" width="300" /></a>

[<img src="screenshots/introduction.gif" width="225" height="400">](screenshots/raw/introduction.gif)
[<img src="screenshots/main-panel.png"/>](screenshots/raw/main-panel.png)
[<img src="screenshots/features-2.png"/>](screenshots/raw/features-2.png)

## 実装されている機能

- [便利なHTTPスニファ](#アプリのネットワークトラフィックを記録)
- [実装されるアクティビティはいつでも３D化できます](#アクティビティを3D化する)
- [SharedPreferencesをランタイム時に簡単的に編集できます](#SharedPreferencesを編集する)
- [ランタイム時にStrictモードをオン・オブにするのを可能とさせてます](#Strictモードで開発)
- [早くメモリリークを発見できて、そして回避できます](#leak-canaryを使用する)
- [UIスレッドのフリーズを起こすコードを見つけます](#uiフリーズを排除する)
- [いずれのアクティビティを簡単にアクセスできます](#アクティビティをいつでも起動する)

#### アプリのネットワークトラフィックを記録
この機能は、デバッグボットルがHTTPリクエストとリスポンスをログファイルに書き込む。記録されたログファイルはいつでも見えます。

[<img src="screenshots/network-sniffer-1.png"/>](screenshots/raw/network-sniffer-1.png)
[<img src="screenshots/network-sniffer-2.png"/>](screenshots/raw/network-sniffer-2.png)

#### アクティビティを3D化する
View のデバッグ用途として、画面をグリグリすると 3DCG みたいに View のヒエラルキーを見ることができるようになるツールです。

[<img src="screenshots/scalpel-view.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/scalpel-view.gif" width="225" height="400" />](screenshots/raw/scalpel-view.gif)

#### SharedPreferencesを編集する
アプリが使ってるすべてのSharedPreferencesをランタイム時に編集する。

[<img src="screenshots/shared-preferences-editor-1.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/shared-preferences-editor-2.png"/>](screenshots/raw/network-sniffer-2.png)

#### Strictモードで開発
StrictModeはアプリケーションの動作をもっさりさせる原因となる、 ディスクやネットワークへのアクセスを検知するための仕組みです。使う方とかを[オフィシャルサイト](https://developer.android.com/reference/android/os/StrictMode.html)で見えます。

#### Leak Canaryを使用する
Squareのメモリリークを検出するライブラリ Leak Canary を通じて、メモリリークを調査できます。デバッグボトルはすでにLeak Canaryを含んでいるから、その機能は使えます。もっと詳しくは[こちらで](https://github.com/square/leakcanary/wiki/FAQ)。

#### UIフリーズを排除する
一旦UIフリーズが発生となると、デバッグボトルはノティファイで通知して、同じ時にログファイルを作成します。開発者はログファイルで、フリーズの原因を知り、排除するのは可能となります。

[<img src="screenshots/ui-blocks.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/block-canary-demo.gif" width="225" height="400" />](screenshots/raw/block-canary-demo.gif)

#### アクティビティをいつでも起動する
デバッグボトルはシンプルなエントリーを提供し、アクティビティやRunnableはそのエントリーを通じて起動できます。エントリーは３つあります：
* すべてのアクティビティエントリー
* カストマイズインテントエントリー
* カストマイズRunnableエントリー

[<img src="screenshots/all-activities.png"/>](screenshots/raw/network-sniffer-2.png)
[<img src="screenshots/run-activity-with-intent.gif" width="225" height="400" />](screenshots/raw/run-activity-with-intent.gif)

## 構築するには

#### 1. Gradleプロジェクトファイルを構成する
まずはスナップショットリポジトリを配置します。
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
アプリのプライマリモジュールによるGradleファイルに依頼環境を導入します。

```gradle
dependencies {
    debugCompile 'com.exyui.android:debug-bottle-runtime:1.0.0EAP-SNAPSHOT'

    // Javaの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0EAP-SNAPSHOT'

    // Kotlinの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0EAP-SNAPSHOT'

    compile 'com.android.support:appcompat-v7:23.2.0+'
    compile 'com.android.support:support-v4:23.2.0+'
}
```

Specially, Debug Bottle not only support API 23+, but also 22. To support API 22, please add dependencies like this:
```gradle
dependencies {
    debugCompile 'com.exyui.android:debug-bottle-runtime:1.0.0-support22-EAP-SNAPSHOT'

    // Javaの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0-support22-EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0-support22-EAP-SNAPSHOT'

    // Kotlinの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0-support22-EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0-support22-EAP-SNAPSHOT'

    compile 'com.android.support:appcompat-v7:22+'
}
```

To support API 23, add dependencies like this:
```gradle
dependencies {
    debugCompile 'com.exyui.android:debug-bottle-runtime:1.0.0-support23-EAP-SNAPSHOT'

    // Javaの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0-support23-EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-java:1.0.0-support23-EAP-SNAPSHOT'

    // Kotlinの場合はこうして構築します
    releaseCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0-support23-EAP-SNAPSHOT'
    testCompile 'com.exyui.android:debug-bottle-noop-kotlin:1.0.0-support23-EAP-SNAPSHOT'

    compile 'com.android.support:appcompat-v7:23+'
}
```

#### 2. Manifestに加入
Manifestにデバッグボトルのプライマリアクティビティを加入します。
```xml
<activity
    android:name="com.exyui.android.debugbottle.components.__DTDrawerActivity"
    android:theme="@style/Theme.AppCompat.Light"
    android:label="ほげほげツール"/>
```
「ほげほげツール」はこのツールの名前、欲しいネームを付けてよろしい、ただし空きでは行けません。

#### 3. Applicationにデバッグボトルを注入
まずは「BlockCanaryContext」を実装します。
```java
public class AppBlockCanaryContext extends BlockCanaryContext {...}
```

そしてデバッグボトルをApplicationに注入します。
```java
public class MyApplication extends Application{
    @Override
    public void onCreate() {
        super.onCreate();
        Installer.install(this)
            .setBlockCanary(AppBlockCanaryContext(this))
            .setOkHttpClient(httpClient)
            .setInjector("your.package.injector.ContentInjector")
            .setPackageName("your.package")
            .enable()
            .run();
    }
}
```
Kotlinを使ってる場合は、以下の方法で注入します。
```kotlin
class MyApplication: Application() {
    override fun onCreate() {
        super.onCreate()
        Installer.install(this)
            .setBlockCanary(AppBlockCanaryContext(this))
            .setOkHttpClient(httpClient)
            .setInjector("your.package.injector.ContentInjector")
            .setPackageName("your.package")
            .enable()
            .run()
    }
}
```

## リンク先
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
