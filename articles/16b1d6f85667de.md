---
title: "NativeScript-Vueで文章入力フォームの高さ初期値を変える"
emoji: "🎴"
type: "tech"
topics:
  - "nativescript"
  - "vue"
published: true
published_at: "2020-12-11 16:33"
---

# 背景

コメントを入力する欄をつくりたい。`<TextField />`([公式doc](https://nativescript-vue.org/en/docs/elements/components/text-field/))を使っていたが、複数行になったときに拡大していく挙動に対応していないようなので`<TextView />`([公式doc](https://nativescript-vue.org/en/docs/elements/components/text-view/))に切り替える。

# 問題

しかし`<TextView />`は初期の高さが6行もある。以下のGIFで、入力していくにつれどんどん拡大していく期待通りの挙動と、最初のボックスが6行もある期待はずれの見た目が確認できる：

![](https://storage.googleapis.com/zenn-user-upload/r6rg1xuq9fi899jhs7rwwweozjp4)

最初は1行にしたいのだが、CSSで無理やり`height: 32;`などとすると今度は入力に連れ拡大する挙動が失われてしまう。どうすれば…

# 解決

`height: 32;`ではなく`min-height: 32;`にすればいいだけだった。次のようになる。
![](https://storage.googleapis.com/zenn-user-upload/2fnlb487hwhwg0vokqqxj8agnqi6)