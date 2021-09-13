# SwitchCompatにtrackのスタイルが適用できない

![styled switch](styled-switch.png)

こういうSwitchを作ろうとしたときのstyle定義をしてもtrack部分(スライダー部分)に
Styleが適用されなかったときのメモです。

!!! Env "環境"
    - compileSdkVersion 30
    - androidx.appcompat:appcompat:1.3.1

## NGケース

```xml
<style name="HassakuLab.Switch.Small" parent="Widget.AppCompat.CompoundButton.Switch">
    <item name="android:switchMinWidth">40dp</item>
    <item name="android:track">@drawable/switch_track_small</item>
    <item name="android:thumb">@drawable/switch_thumb_small</item>
</style>
```

## OKケース
SwitchCompatのtrack属性は`android:track`を参照していないみたいなので、
`android:track`と指定しているところを`track`に書き換えると適用されました。

```xml
<style name="HassakuLab.Switch.Small" parent="Widget.AppCompat.CompoundButton.Switch">
    <item name="android:switchMinWidth">40dp</item>
    <item name="track">@drawable/switch_track_small</item>
    <item name="android:thumb">@drawable/switch_thumb_small</item>
</style>
```
