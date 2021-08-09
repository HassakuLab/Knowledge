---
title: ソフトキーのDONEを押したときにEditTextのフォーカスを外す
---

Android開発において、EditTextの編集後に1回だけイベントを発火したい等の理由で、`OnFocusChangeListener`を設定しているときなど、ソフトキーの操作でEditTextからフォーカスを外したいときがあります。その方法です。

!!! Env "環境"
    - compileSdkVersion 30

## 方法

当該操作時に一時的に`isFocusable`をfalseに設定してあげればフォーカスは外れます。

```kotlin
findViewById<EditText>(R.id.some_edit).also { editText ->
    editText.onFocusChangeListener = object:View.OnFocusChangeListener{
        override fun onFocusChange(view: View?, hasFocus: Boolean) {
            //  do domething
        }
    }

    editText.setOnKeyListener { _, keyCode, _ ->
        if(keyCode == KeyEvent.KEYCODE_ENTER){
            (getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager).also{ imm ->
                imm.hideSoftInputFromWindow(currentFocus!!.windowToken, InputMethodManager.HIDE_NOT_ALWAYS)
            }
            editText.isFocusable = false
            editText.isFocusableInTouchMode = true
            true
        }
        else{
            false    //  raise
        }
    }
}
```