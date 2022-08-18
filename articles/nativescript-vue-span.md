---
title: "NativeScript-Vueでの行間の設定はLabel>Spanを使うべき"
emoji: "🎴"
type: "tech"
topics:
  - "css"
  - "nativescript"
  - "scss"
  - "vue"
published: true
published_at: "2021-03-02 16:56"
---

# NativeScriptではCSSは完全にサポートされていない

NativeScriptはCSSを完全にサポートしておらず、いくつか使えないプロパティがある。最新のサポートされたプロパティは[公式の一覧](https://docs.nativescript.org/ui/styling#supported-css-properties)を参照すればわかる。今回はその中でも挙動がわかりにくい行間の設定(`line-height`)について取り上げる。

NativeScriptではテキストの行間をCSSと同様に[line-heightで設定できる](https://docs.nativescript.org/ui/styling)が、挙動は[WebでのCSSのそれ](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)とは大きく異なる。


## Webでの行間の設定の仕方

CSSで`font-size: 16px`で`line-height: 1`とすれば行と行の間の空白は0pxになり、`line-height: 1.5`とすれば`font-size * (line-height - 1) = 8px`が空白になる。`line-height: 200%`とか`line-height: 2em`とすれば`line-height: 2`と同義になる。また`line-height: 0.8`などとすればギチギチに行間を詰めることができる。


## NativeScriptでの行間の設定の仕方

(私は[NativeScript-Vue](https://nativescript-vue.org/)を使っているので他のNativeScriptでの挙動について調べてはいないが、NativeScript-Vueのドキュメントでは`line-height`については一言も触れられていないため同じなのではないかと憶測する)

NativeScriptでは、複数行にわたる文章では`<Label>`を使う。`<Label>`コンポーネント全体の文字色を変更する場合、`<Label class="red" text="hello"　textWrap="true" />`とする。もしそのウィジェットの一部のみを赤字にしたい場合、

```html
<Label>
	<FormattedString>
		<Span class="red" text="this text will be red." />
	</FormattedString>
</Label>
```
などとすればよい、と[公式のFormatted Stringについてのドキュメントに書いてある](https://docs.nativescript.org/angular/ui/ng-components/formatted-string)。ではその調子で`line-height`を変えてみよう！`line-height: 1.5`にすればまぁちょうどよくなるやろ。

### line-height: 1.5ではダメ

tl;dr: `line-height: 1.5;`と同等の効果を得たい場合、`font-size: 16px`であれば`line-height: 8;`とする。ただし`<Label>`のみの場合。

```vue
<template>
  <Page>
    <ScrollView>
      <StackLayout>
        <Label :text="text" class="a" textWrap="true" />
      </StackLayout>
    </ScrollView>
  </Page>
</template>

<script>
export default {
  data() {
    return {
      text:
        'Hurricane Dorian was the strongest hurricane to affect the Bahamas, causing catastrophic damage in September 2019.',
    }
  },
}
</script>

<style lang="scss" scoped>
.a {
  background-color: #eee;
  font-size: 16;
  line-height: 1.5;
  margin: 16 0;
}
</style>
```
![](https://storage.googleapis.com/zenn-user-upload/e70i35xkdcoavc9oytauaseapcxi)

...アレ？なんかおかしい。種明かしをすると、NativeScriptでの`line-height`は、(`<Label>`に適用された場合（※ここがあとで重要になる）)行と行の間の空白をdip(device independent pixels, NativeScriptで基本となる単位と理解している)で表したものであって、Webにおける`line-height`のような倍率ではない。`line-height: 1.5em`としてみたり、`font-size: 16px`と単位を明記してみたりしても無駄である。ちなみに`line-height: 150%`などとすると更に謎の行間になる。なので、Webにおける`line-height: xxx`と同等のものを設定したい場合、`font-size`に`(xxx - 1)`を掛けた値にする必要がある。
```
<style lang="scss" scoped>
.a {
  background-color: #eee;
  font-size: 16;
  line-height: 8;
  margin: 16 0;
}
</style>
```

![](https://storage.googleapis.com/zenn-user-upload/672b9rvf0h3yq6e2by2by4uy5tlr)

### 行間を詰めることはできない（私の知る限り）

WebのCSSであれば`line-height: 0.8;`などとすることで詰められる。長い文章ではあまり多用することはないが、見出しやタイトルなどで使うこともあるだろう。しかしNativeScriptでは↑の仕様のうえ、負の値を`line-height`に設定しても`line-height: 0`と同等に振る舞うため、行間を詰めることはできない（多分）。NativeScriptのSlackでも質問したが解決しなかった：
![](https://storage.googleapis.com/zenn-user-upload/ld09uhp37zvi31yud7rlyuo2rvba)

# LabelのなかにSpanを入れると挙動が変わる

この記事の主題。`<Label>`のなかに`<Span>`を入れると`line-height`の挙動が変わる。以下では`class-a`というクラスに`font-size: 16;`かつ`line-height: 24;`というスタイルをあてているので、LabelだけであればWebでのCSS換算で`line-height: 2.5`のとても大きな行間となる（上）。しかしそのなかに`<Span>`タグを入れると行間はWebでのCSS換算で`line-height: 1.5`のわりとちょうどよい行間となる(中)。そしてSpanタグのほうに同じクラスをあてると`line-height`プロパティは無視される(下)。

```vue
<template>
  <Page>
    <ScrollView>
      <StackLayout>
        <Label
          class="class-a"
          :text="'Label.class-a\n' + text"
          textWrap="true"
        />
        <Label textWrap="true" class="class-a">
          <Span class="section" text="Label.class-a>Span" />
          <Span :text="'\n' + text" />
        </Label>
        <Label textWrap="true">
          <Span class="section" text="Label>Span.class-a" />
          <Span class="class-a" :text="'\n' + text" />
        </Label>
      </StackLayout>
    </ScrollView>
  </Page>
</template>

<script>
export default {
  data() {
    return {
      text:
        'Hurricane Dorian was the strongest hurricane to affect the Bahamas, causing catastrophic damage in September 2019.',
    }
  },
}
</script>

<style lang="scss" scoped>
.class-a {
  margin: 16 0;
  background-color: #eee;
  font-size: 16;
  line-height: 24;
}

.section {
  color: #a8d9f5;
  background-color: #180630;
}
</style>
```

![](https://storage.googleapis.com/zenn-user-upload/dx0lnjzbvyhh3zcaq67rlmn51q9k)

つまり、どうも**Labelコンポーネントにおいては、行間はfont-size * (line-height)で計算される**が、**Labelコンポーネント内にSpanコンポーネントがある場合においては、行間はfont-size * (line-height - 1)で計算される**うえに、**Spanコンポーネントにline-heightのプロパティを指定しても、反映されない**らしいのだ。厄介すぎる。さらに、`<FormattedString>`は（少なくともNS-Vueにおいては）なしでも同じ動作をする。このややこしさを一覧にしてみたので、各自コピペして数値や包含関係をいじってみてほしい：

```vue
<template>
  <Page>
    <ScrollView>
      <StackLayout>
        <Label :text="'Label\n' + text" textWrap="true" />
        <Label class="class-a" :text="'Label.class-a\n' + text" textWrap="true" />
        <Label textWrap="true">
          <Span class="section" text="Label>Span" />
          <Span :text="'\n' + text" />
        </Label>
        <Label textWrap="true">
          <Span class="section" text="Label>Span.class-a" />
          <Span class="class-a" :text="'\n' + text" />
        </Label>
        <Label textWrap="true" class="class-a">
          <Span class="section" text="Label.class-a>Span" />
          <Span  :text="'\n' + text" />
        </Label>
        
        <Label textWrap="true">
          <Span class="section" text="Label>Span.class-a" />
          <Span class="class-a" :text="'\n' + text" />
        </Label>
        <Label class="class-a" textWrap="true">
          <Span class="section" text="Label.class-a>Span.class-a" />
          <Span class="class-a" :text="' \n' + text" />
        </Label>
        <Label class="class-a" textWrap="true">
          <FormattedString>
          <Span class="section" text="Label.class-a>FormattedString>Span" />
          <Span :text="'\n' + text" />
          </FormattedString>
        </Label>   
        <Label textWrap="true">
          <FormattedString class="class-a" >
            <Span class="section" text="Label>FormattedString.class-a>Span" />
            <Span  :text="'\n' + text" />
          </FormattedString>
        </Label>   
        <Label textWrap="true">
          <FormattedString>
            <Span class="section" text="Label>FormattedString>Span.class-a" />
          <Span  class="class-a" :text="' \n' + text" />
          </FormattedString>
        </Label>   
        </StackLayout>
      </ScrollView>
    </Page>
</template>

<script >
  export default {
    data() {
      return {
        text: "Hurricane Dorian was the strongest hurricane to affect the Bahamas, causing catastrophic damage in September 2019."
      }
    }
  }
</script>

<style lang="scss" scoped>
.class-a {
  margin: 16 0;
  background-color: #eee;
  font-size: 16;
  line-height: 24;
}
.section {
  color: #a8d9f5;
  background-color: #180630;
}
</style>
```
![](https://storage.googleapis.com/zenn-user-upload/uzxquo2ryuqbgnbrsrmphm94k15l)
![](https://storage.googleapis.com/zenn-user-upload/j7qtfhezxy1evqeqkeobsixy7et5)

# どうすればいいのか

わからない。私は今の時点では次のようにしようと思っている。

複数行に渡らない、行間を設定することがありえない、文章の一部のスタイルを変えることがありえない`<Label>`コンポーネントはそのまま使う。そこに`line-height`プロパティは出現し得ず、他の`margin`とか`background-color`とかのプロパティを編集するぶんにはこれで問題ない。NativeScriptには「なるべくフラットにせよ（コンポーネントをネストすればするほど表示が重くなる）」という鉄則があり、無駄に子コンポーネントを持つべきでないため。

`<Label>`コンポーネントの文章の一部または全体の行間やスタイルを変更したくなったら、`<Span>`コンポーネントを子コンポーネントとして持つようにする。そして`line-height`は行間ではなく、行も含めた値(`font-size: 16`ならたとえば`line-height: 24`だし、`font-size: 72`ならたとえば`line-height: 84`とかが実用的な値となる)を設定する。

バグの類だと思うのだが、`nativescript-vue@^2.8.3`での挙動は以上の通り。南無。
