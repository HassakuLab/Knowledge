---
title: イベントを発火せずにEditTextへテキストを設定する
---

Android開発において、EditTextの値の変更を監視するとき、TextWatcherを設定すると思います。
ところが設定したTextWatcherにイベントを発火せずに`setText()`しなければいけない機会があったのでその方法です。

!!! Env "環境"
    - compileSdkVersion 30

## 方法

`setText()`するときだけ一時的にTextWatcherを削除してあげればイベントは発火しません。設定されているTextWatcherを取得する方法が無いのでちょっとめんどくさいですね。

```kotlin
class ExtendedEditText: AppCompatEditText {

    private var watcher: TextWatcher? = null

    constructor(context: Context): super(context)
    constructor(context: Context, attrs: AttributeSet?): super(context, attrs)
    constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int): super(context, attrs, defStyleAttr)

    override fun addTextChangedListener(watcher: TextWatcher?) {
        super.addTextChangedListener(watcher)

        this.watcher = watcher
    }

    /**
     * イベントを発行せずにテキストを設定する
     */
    fun setTextWithoutWatch(s: CharSequence){
        removeTextChangedListener(watcher)
        this.setText(s)
        addTextChangedListener(watcher)
    }
}
```
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    findViewById<ExtendedEditText>(R.id.some_edit).apply{
        addTextChangedListener(object: TextWatcher{
            override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
                //  do something
            }
            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                //  do something
            }
            override fun afterTextChanged(s: Editable?) {
                //  do something
            }
        })
    }
}
```