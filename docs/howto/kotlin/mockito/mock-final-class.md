# finalクラスをmockする

mockitoを使ってkotlinのclassをmockしようとすると、
kotlinのクラスはデフォルトでfinalなのでmockできません。
その解決法です。

!!! Env "環境"
    - mockito-kotlin 3.2.0

## 方法

[Mockito (Mockito 3.12.0 API)](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#39) にあるとおり、
mock makerを有効にします。

`src/test/resources/mockito-extensions/org.mockito.plugins.MockMaker`ファイルを作成して下記1行を書きます

```
mock-maker-inline
```
