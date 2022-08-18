---
title: "NativeScript-VueでiOSのStatus Bar背景色を強引に変える"
emoji: "🎴"
type: "tech"
topics:
  - "nativescript"
  - "vue"
published: true
published_at: "2020-11-18 14:39"
---

# 動機


![](https://storage.googleapis.com/zenn-user-upload/werxmjbuhdjaow2z4bmrri10v04v)
[iOSのstatus bar](https://developer.apple.com/design/human-interface-guidelines/ios/bars/status-bars/)の背景を黒にしたいが、ステータスバー以外の背景色は白にしたい。

# 公式ドキュメントを読めばわかること

NativeScriptでは直接ステータスバーの色を変更することはできない。できることは公式の[ドキュメント](https://docs.nativescript.org/angular/ui/change-status-bar-style-ios)に列挙されている。

**時刻やキャリアなど内容を白くするには**

`app/App_Resources/iOS/Info.plist`の最後の方に`UIStatusBarStyle`をLightに指定すればよい。これは以下の方法を用いる場合でも共通。

**背景色を変えるには**

1. デフォルトのActionBarを利用**している**場合、ActionBarの色を変えるだけでよく比較的簡単に実装できる。`<ActionBar backgroundColor="green">`とすればよい。
1. ActionBarを利用**していない**場合、`<Page>`に`backgroundSpanUnderStatusBar="true" backgroundColor="red"`を追加すればその`backgroundColor`になる。

# 問題

私がいま取り組んでいるプロジェクトではデフォルトのActionBarを利用していないので、2.の方法を使う必要がある。`<Page>`で`backgroundColor="black"`を指定しても、その直下のレイアウト要素(`GridLayout`や`StackLayout`のたぐい）で`backgroundColor="white"`などとすると、「ん？アンタPageで`black`を指定してるけど、結局直下のレンダリングする必要のあるものの色は`white`なのね？じゃあ`black`ではなく`white`をstatusbarのunderにspanするよ、アンタが指定したとおりにね」と白になってしまう。

# 解決

最初にレンダリングされる要素の背景が黒であればそれをステータスバーに引き伸ばしてくれるようなので、強引に1pxの高さの黒背景エレメントをビューの一番上にかませることで解決した。

```vue
<Page
    actionBarHidden="true"
    backgroundSpanUnderStatusBar="true"
  >
    <StackLayout class="ArticleDetails">
      <Label text=" " class="ArticleDetails__pseudo-before" />
      <GridLayout
        class="ArticleDetails__inner"
      >
```

とした。

`style`では以下のように指定した：

```scss
<style lang="scss" scoped>
.ArticleDetails {
  background-color: black;
  &__pseudo-before {
    color: black;
    background-color: black;
    height: 1px;
  }
  &__inner {
    background-color: white;
  }
```

`<Label text=" " class="ArticleDetails__pseudo-before" />`が強引に追加した1pxの高さの要素で、テキストは適当にスペースを入れ、これの背景色を黒に指定する。文字色も一応`black`を指定した。

この`<Label>`なしで`<StackLayout>`の背景色を黒に指定しても、直下の`<GridLayout>`の背景色を白にしてもうまくいかない。また`<StackLayout>`を省略して`<Label>`と`<GridLayout>`を同列にしてもうまくいかない。もう少し簡単に書ける気がするのだが、ひとまず見た目は想定通りになったので当面はこれでやってみる。

## any help is appreciated

[nativescript-statusbar](https://market.nativescript.org/plugins/nativescript-statusbar/)というプラグインで直接dark色を選べるようになるようなのだが、NS7にアップグレードしたばかりだしこれ以上プラグイン増やすのもな…と思い試さなかった。もしかするとこれでうまくやれるのかもしれない。怠惰。

強引すぎるのでもっと賢い方法を知っている方は教えて下さい。