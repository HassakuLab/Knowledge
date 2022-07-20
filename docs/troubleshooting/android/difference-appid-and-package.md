# ApplicationIdとpackage名が異なるときにリソースへの参照がunresolved referenceとしてマークされる

Androidネイティブ開発で、ApplicationIdとpackage名が異なる状態のとき、RとBuildConfigへの参照がunresolved referenceとされ、補完もできないという状態を解消する方法です。

!!! Env "環境"
    - compileSdkVersion 31
    - buildToolsVersion 30.0.3
    - AndroidStudio 2021.1.1 Patch 2

## 現象

gradleビルドスクリプトの一部
```build.gradle.kts

android {
    compileSdk = 31
    buildToolsVersion = "30.0.3"

    defaultConfig {
        applicationId = "com.hassakulab.sample"
        minSdk = 23
        targetSdk = 31
        versionCode = 1
        versionName = "0.0.1"
        //  略...
    }
    //  略...
}
```

ソースコードのpackage宣言部分
```kotlin
package com.hassakulab.foo.bar
//  ...
import com.hassakulab.foo.R //  ここがunresolved reference
//  ...
```

package名が`com.hassakulab.foo` applicationIdが`com.hassakulab.sample` で異なる。
エディタはunresolved referenceと解釈し、補完できない。
コンパイルは通る。

## 修正

ビルドスクリプトでnamespaceを指定する。
namespaceはRとBuildConfigのpackage名として利用される。
```build.gradle.kts

android {
    namespace = "com.hassakulab.foo"
    compileSdk = 31
    buildToolsVersion = "30.0.3"

    defaultConfig {
        applicationId = "com.hassakulab.sample"
        minSdk = 23
        targetSdk = 31
        versionCode = 1
        versionName = "0.0.1"
        //  略...
    }
    //  略...
}
```
