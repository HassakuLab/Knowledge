# registerForActivityResult利用時にIllegalStateException

AndroidでActivityResultApi[^1]の`registerForActivityResult`を使う際、下記の実行時例外が発生したのでその対処方です。

```
java.lang.IllegalStateException: LifecycleOwner xxx is attempting to register while current state is RESUMED. LifecycleOwners must call register before they are STARTED.
```

[^1]: 結果を返すActivityのための新しいAPI [アクティビティの結果を取得する  |  Android デベロッパー  |  Android Developers](https://developer.android.com/training/basics/intents/result?hl=ja)

!!! Env "環境"
    - compileSdkVersion 30
    - androidx.activity:activity-ktx 1.2.4
    - androidx.fragment:fragment-ktx 1.3.6

## 発生条件

エラーメッセージの通り、STARTEDより後に`registerForActivityResult`を呼び出すとダメです。例えばボタンのクリック時なんかに呼び出そうとすると、クリック時のActivityのlifecycleはRESUMEDなので冒頭のようなエラーメッセージが出ます。

```kotlin
class FooActivity: AppCompatActivity{

  //  ...

  override fun onCreate(){
    super.onCreate()

    findViewById<Button>(R.id.button_bar).also{ button ->
      button.setOnClickListener {
        //  ここが実行されるのはRESUMEDのとき
        registerForActivityResult(StartActivityForResult()){ result ->
          //  handle result
        }.run{
          launch(Intent(this@FooActivity, ReturnResultActivity::class.java))
        }
      }
    }
  }
}
```

## 対応方法

エラーメッセージによると`lifecycleOwner`の登録はSTARTEDより前に済ませてくださいねーって書いてあるので、`ActivityResultLauncher`をクラスフィールドに出してあげます。
エラーメッセージが親切ですね。

```kotlin

class FooActivity: AppCompatActivity{

  //  クラスフィールドとしてActivityResultLauncherを出してあげる
  val launcher = registerForActivityResult(StartActivityForResult()){ result ->
    //  handle result
  }

  override fun onCreate(){
    super.onCreate()

    findViewById<Button>(R.id.button_bar).also{ button ->
      button.setOnClickListener {
        launcher.launch(Intent(this@FooActivity, ReturnResultActivity::class.java))
      }
    }
  }
}
```
