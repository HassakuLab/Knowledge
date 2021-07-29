---
title: Kotlin利用時にAnnotationによってコードが生成されない
---

例えば、androidx.room利用時に
```
java.lang.RuntimeException: cannot find implementation for com.hassakulab.sample.XXXDatabase. XXXDatabase_Impl does not exist
```
のようなエラーを吐いたりしますが、これはannotationが処理でコードを生成できていないのが原因なので対応していきましょう。

!!! Env "環境"
    - Gradle 6.9
    - Android Gradle Plugin 4.2.2


## 対応方法

build.gradleで`annotationProcessor`を使っている箇所を`kapt`に置き換えます。  
以下コードはKotlinですがGroovyでも同じですね。

```kotlin
plugins {
    id("com.android.application")
    id("kotlin-android")
    id("kotlin-kapt")             //  追加
}

dependencies{
  
  //  ...中略

  implementation("androidx.room:room-runtime:2.3.0")
  implementation("androidx.room:room-ktx:2.3.0")
  //  annotationProcessorを使っているところをkaptに変更
  //  annotationProcessor("androidx.room:room-compiler:2.3.0")
  kapt("androidx.room:room-compiler:2.3.0")

}
```

## 補足1

`annotationProcessor`を利用しているとビルド時に警告が出ます。
```
app: 'annotationProcessor' dependencies won't be recognized as kapt annotation processors. Please change the configuration name to 'kapt' for these artifacts: 'androidx.room:room-compiler:2.3.0'.
```
Androidの公式ガイドでは`annotationProcessor`使っているので、そのまま転記していると見落としちゃうんですよね。なまじコンパイルが通るだけに。

[Room を使用してローカル データベースにデータを保存する  |  Android デベロッパー  |  Android Developers](https://developer.android.com/training/data-storage/room?hl=ja#groovy)

上手くいかないときはビルドログにちゃんと目を通して、警告ハンドリングもしっかりしましょうということですね。

## 補足2

kapt is 何ってことなんですが、Java の Pluggable Annotation Processing API[^1]をKotlinから叩くための、Kotlinコンパイラのプラグインってことらしいです。Kotlin Annotation Processing Tool でkapt。

[^1]: [The Java Community Process(SM) Program - JSRs: Java Specification Requests - detail JSR# 269](https://jcp.org/en/jsr/detail?id=269)

[Using kapt | Kotlin](https://kotlinlang.org/docs/kapt.html) 

