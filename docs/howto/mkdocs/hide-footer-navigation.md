---
title: フッターナビゲーションを非表示にする
---

MkDocsでフッターナビゲーションを非表示にする方法です。

これ  
![footer navigation](footer-nav.png)

!!! Env "環境"
    - mkdocs 1.2.1
    - mkdocs-material 7.1.10

[Customization - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/customization/#extending-the-theme)にあるとおり、
themeのオーバーライドで対応可能です。

ここでは "Overriding partials" による方法で上書きします。

## 手順

利用しているテーマの上書き前のhtmlファイルを取得してローカルにコピーします。
今回はfooterを上書きするので、footer.htmlをコピーして配置します。

```
┣ docs
┗ overrides
  ┗ partials
    ┗ footer.html
```

footer.htmlから`<nav>`タグをまるごと削除します。

最後に、mkdocs.ymlに配置したディレクトリのパスを設定します。
```yaml
theme:
  name: material
  custom_dir: overrides
```
