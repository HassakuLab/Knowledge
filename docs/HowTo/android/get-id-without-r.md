---
title: R.idを使わずにidを解決する
---

Android開発において、BuildVariantでリソースを切り替えて`findViewById`するときなど、`R.id`を使わないidの解決方法が必要になることがあります。  
対応として、idを実行時に解決する方法です。

!!! Env "環境"
    - compileSdkVersion 30

## 方法

`Resources.getIdentifier`[^1]でInt型のidを取得できます。

[^1]: [Resources  |  Android デベロッパー  |  Android Developers](https://developer.android.com/reference/android/content/res/Resources)

```kotlin
/**
 * R.idを使わずに文字列からidを取得する
 * @param idName リソースファイルに記載したIDの文字列
 */
fun getIdFromString(context: Context, idName: String): Int{
  return context.resources.getIdentifier(idName, "id", context.packageName)
}

class FooActivity: AppCompatActivity{
  //  ...

  override fun onCreate(){
    super.onCreate()

    if(BuildConfig.FLAVOR == "dev"){
      val id = getIdFromString(this, "_dev_button_bar") //  idの取得

      findViewById<Button>(id).also{ button ->
        //  ...
      }
    }

  }
}

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".FooActivity">

    <Button
        android:id="@+id/_dev_button_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="button" />

</FrameLayout>
```

