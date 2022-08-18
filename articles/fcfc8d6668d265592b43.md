---
title: "NativeScript-VueのMultiDrawerで左端30pxがタップできない問題を強引に解決する"
emoji: "🎴"
type: "tech"
topics:
  - "nativescript"
  - "vue"
published: true
published_at: "2020-11-25 17:06"
---

# 背景

[nativescript-vue/nativescript-vue-multi-drawer](https://github.com/nativescript-vue/nativescript-vue-multi-drawer)を左端に配置していた。デフォルトオプション`defaultOptions`を[index.js](https://github.com/nativescript-vue/nativescript-vue-multi-drawer/blob/master/index.js)で確認すれば分かるが、完全に画面の左端からスワイプしなくても、左端30pxの部分からスワイプすればドロワーが引き出せるような設定になっている。

```js

export let defaultOptions = {
  debug: false,
  backdropColor: 'rgba(0, 0, 0, 0.7)',
  left: {
    width: '70%',
    height: null,
    backgroundColor: '#ffffff',
    canSwipeOpen: true,
    swipeOpenTriggerWidth: 30,
    swipeOpenTriggerHeight: null,
    swipeOpenTriggerMinDrag: 50,
    swipeCloseTriggerMinDrag: 50,
    swipeOpenTriggerProperties: {},
    animation: {
      openDuration: 300,
      closeDuration: 300,
    },
    translationOffsetMultiplier: -1,
    axis: 'X',
  },
  // ...
}
  ```

# 問題

その左端30pxでのインタラクションはすべてこの子に持っていかれてしまっているようで、この部分にボタンやリンクなどを配置して`@tap`を設定しても反応しない。

# 解決

できれば左端でのサイドバー用スワイプとリンク用タップを両立したかったが、とりあえず解決してしまいたかったので`swipeOpenTriggerWidth: 30,`を`1`に書き換えた。`0`にすると今度は全くサイドバーが引き出せなくなる。

方法は2通りある。

1. サイドドロワーの設定をどれもリアクティブに変更する予定がなければ、アプリの`main.js`で`Vue.use(MultiDrawer)`を`Vue.use(MultiDrawer, {left:{swipeOpenTriggerWidth:1,}})`とする
2. サイドドロワーの設定をリアクティブに変更する予定があれば、[NativeScript-Vueでサイドドロワーを出すビューを限定する](https://zenn.dev/xerroxcopy/articles/629b2f188db7c7b8074e)を参考に`<MultiDrawer :options="drawerOptions">`などとして設定する

# 改善したい

簡単だがやはり理想的には「どこからでも左にスワイプすれば左サイドドロワーが引き出せる」＋「その下の要素をどれでもタップできる」という今風の操作がしたい。やり方を知り次第続きを書きます。
