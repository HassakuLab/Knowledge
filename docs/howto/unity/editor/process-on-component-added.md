---
title: コンポーネントを追加したときに処理をする
---

UnityでInspectorからコンポーネントを追加したときに何かしら処理をする方法です。

!!! Env "環境"
    - Unity 2021.2.5f1

## 方法

`Reset()`イベントはコンポーネントを追加したときに実行されます。

```csharp
[RequireComponent(typeof(Rigidbody2D))]
public class KinematicItem: MonoBehaviour{
  
  private void Reset(){
    Rigidbody2D rig = GetComponent<Rigidbody2D>();bodyType = RigidbodyType2D.Kinematic;
  }

}
```