---
title: build.gradleでkotlin-stdlibへの依存間関係を追加しているとビルドに失敗する
---

表題の通り、kotlin-stdlib への依存関係をbuild.gradleに書いていると以下のようなエラーを吐いてビルドが失敗します。kotlin-stdlibの互換性が無いみたいな内容ですね。

```
Execution failed for task ':app:mergeExtDexDevelopDebug'.
> Could not resolve all files for configuration ':app:debugRuntimeClasspath'.
   > Failed to transform koin-android-compat-3.1.2.aar (io.insert-koin:koin-android-compat:3.1.2) to match attributes {artifactType=android-dex, asm-transformed-variant=NONE, dexing-enable-desugaring=true, dexing-incremental-transform=true, dexing-is-debuggable=true, dexing-min-sdk=21, org.gradle.category=library, org.gradle.dependency.bundling=external, org.gradle.libraryelements=aar, org.gradle.status=release, org.gradle.usage=java-runtime}.
      > Could not resolve all files for configuration ':app:debugRuntimeClasspath'.
         > No variants of org.jetbrains.kotlin:kotlin-stdlib:1.5.10 match the consumer attributes:
             - org.jetbrains.kotlin:kotlin-stdlib:1.5.10:
                 - Incompatible because this component declares attribute 'artifactType' with value '732c52d3' and the consumer needed attribute 'artifactType' with value 'android-classes-jar'
                 - Other compatible attributes:
                     - Doesn't say anything about asm-transformed-variant (required 'NONE')
                     ...
```

!!! Env "環境"
    - Kotlin 1.5.21
    - Gradle 6.9
    - kotlin-gradle-plugin 1.5.21

## 対応

kotlin-stdlibへの依存関係を削除します。

```kotlin
dependencies{
  
  //  要らないので消す
  // implementation("org.jetbrains.kotlin:kotlin-stdlib:${rootProject.extra("kotlin_version")}")

}
```

Kotlin 1.4.0 からkotlin-stdlibへの参照が自動的に追加されるようですね。[^1]
自動で追加されるkotlin-stdlibよりも明示的に依存関係を追加したkotlin-stdlibの方が優先して使われていたので互換性が無いようなエラーが出たようです。
AndroidStudioが自動的に追加するので意図して削除しないといけないので注意が必要でした。

自動的に追加されるのを無効にするために、以下をgradle.propertiesに追加する方法もあるようですね。

```groovy
kotlin.stdlib.default.dependency=false
```

[^1]: [What's new in Kotlin 1.4 | Kotlin](https://kotlinlang.org/docs/whatsnew14.html#dependency-on-the-standard-library-added-by-default)
