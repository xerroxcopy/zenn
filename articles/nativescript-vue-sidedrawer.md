---
title: "NativeScript-Vueでサイドドロワーを出すビューを限定する"
emoji: "🎴"
type: "tech"
topics:
  - "nativescript"
  - "vue"
  - "nativescriptvue"
published: true
published_at: "2020-11-13 15:05"
---

# 動機・前提

`nativescript-vue-multi-drawer`で左端からスワイプで引き出せるサイドドロワーを作った（RadSideDrawerはバグが多く使いにくい）。サイドドロワーを出したくないページがある（記事の詳細ページ）。しかしデフォルトではどのビューでも左からスワイプすればでてきてしまう。[issue](https://github.com/nativescript-vue/nativescript-vue-multi-drawer/issues/8)などを読むと`main.js`において
```js: main.js
Vue.use(MultiDrawer, {
  left: {
    canSwipeOpen: false,
  },
})
```
などとすればスワイプで開く動作をオフにできる。

# 問題

しかしこれは静的な値で、動的にVuex storeなどで変更することができない。たとえば次のようにすることが考えられる。まずサイドドロワーをオフにしたいビュー(`ArticleDetails.vue`)において、
```vue:ArticleDetails.vue
// ...
mounted() {
    store.commit('drawer/DISABLE_DRAWER')
  },
  destroyed() {
    store.commit('drawer/ENABLE_DRAWER')
  },
// ...
```
として、`drawer`モジュールの`mutations`に`state.drawer.canSwipeOpen`を`true`にする`ENABLE_DRAWER()`と`false`にする`DISABLE_DRAWER()`を用意する：
```js:/store/drawer.js
const state = {
  canSwipeOpen: true,
}
const mutations = {
  ENABLE_DRAWER(state) {
    console.log('enabling drawer again...')
    state.canSwipeOpen = true
  },
  DISABLE_DRAWER(state) {
    console.log('disable drawer because user visited article details.')
    state.canSwipeOpen = false
  },
}
```

そしてこの値がリアクティブに変更されることを利用して、MultiDrawerを呼び出すときのオプションの値を変更する：

```js: main.js
import store from '@/store'
Vue.use(MultiDrawer, {
  left: {
    canSwipeOpen: store.state.drawer.canSwipeOpen,
  },
})
```
しかしこうしても、`drawer`モジュール内の`state`で`canSwipeOpen`の初期値を`false`に指定すると、この値をいかにmutateしようとも全てのページでオフになってしまう。

# 解決

NativeScript-VueのSlackチャンネルで質問したところ、NS-Vueの開発者`rigor789`が丁寧に教えてくれた。
![](https://storage.googleapis.com/zenn-user-upload/ycovv5ibkqm2v1q8ilahhg5yxrp4)

```vue: App.vue
<template>
// ...
  <MultiDrawer :options="drawerOptions" />
// ...
</template>
<script>
// ...
  computed: {
    drawerOptions() {
      console.log('canSwipeOpen???', this.$store.state.drawer.canSwipeOpen)
      return {
        left: { canSwipeOpen: this.$store.state.drawer.canSwipeOpen },
      }
    },
  }
  // ...
}
```
とすればかんたんに動く。`<MultiDrawer>`に`:options=`ができるなんて…聞いてない…そういうもんなの…？