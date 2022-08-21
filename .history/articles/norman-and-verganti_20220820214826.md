---
title: "ナイーブなデザインの進化5-1: Norman & VergantiとLangrishのけんか 1of3"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ナイーブなデザインの進化"]
published: false
---

https://music.apple.com/jp/album/correspondence/985384058?l=en

# はじめに
 
- 初回は太刀川英輔 (2021) [進化思考](https://zenn.dev/minoru_matsui/articles/evolution-thinking) 
- 第二回はLongo, G. (2009) [_Epistemological Turn_](https://zenn.dev/minoru_matsui/articles/epistemological-turn), 
- 第三回はPetroski, H. (1992) _The Evolution of Useful Things_（未完成）,
- 第四回はWhyte, J. (2007) [_Evolutionary theories and design practices_](https://zenn.dev/articles/evolutionary-theories-and-design-practices),
- 第五回は三回にわけてNorman & Verganti (2014)をめぐる議論を紹介する。

まず今回、5ｰ1では1082回も引用されている高名な理論家ふたりによるNorman & Verganti (2014a)^[Norman, D. A., & Verganti, R. (2014a) [_Incremental and Radical Innovation: Design Research vs. Technology and Meaning Change_](http://www.verganti.com/wp-content/uploads/2017/01/NormanVerganti.pdf). Design Issues. 30, (**1**) 78-96]をレビューする。つぎの5-2ではそれに「進化の観点が欠如している」と突っかかるLangrishのコレスポンデンス(2014)^[Langrish, J. Z. (2014) [_A Response to Donald A. Norman and Roberto Verganti’s “Incremental and Radical Innovation: Design Research vs. Technology and Meaning Change,” Design Issues 30, no. 1 (Winter 2014): 78–96_](https://escholarship.org/content/qt4240b4hs/qt4240b4hs_noSplash_bb0523403c2e77e1e619874dc6f3d2ae.pdf). Design Issues, 30(**3**) 104-106. 題名長すぎでは…]をレビューする。さいごの5-3ではさらに、再反論Norman & Verganti (2014b)^[Norman, D. A., & Verganti, R. (2014) [_Hill Climbing and Darwinian Evolution: A Response to John Langrish_](https://escholarship.org/content/qt4240b4hs/qt4240b4hs_noSplash_bb0523403c2e77e1e619874dc6f3d2ae.pdf). Design Issues, 30 (**3**) 106-107]を検討する。

# Incremental and Radical Innovation: Design Research vs. Technology and Meaning Change

## 背景

- ノーマンとヴェルガンティは別々の道を歩んできた。ノーマンはHCDなどと呼ばれるデザイン探索の創始者のひとりである。HCDには共通の周回的なフレームワークがある。周回により教訓を学んでいき、結果が満足できるものになるか、時間がなくなれば周回を終える。
- ノーマンは想定したユーザーに品質をチェックしてもらうと漸進的な`incremental`改善が見込めることに気づいた。また、これが局所最適解を探索する[山登り法](https://ja.wikipedia.org/wiki/%E5%B1%B1%E7%99%BB%E3%82%8A%E6%B3%95)の一種であることにも。山登り法をデザインに適用する：多次元の丘を考えてみよう。高さが製品の品質をあらわす。高さ以外の次元の座標はデザインの選択肢`choices`であり、多数のデザイン上のパラメータに対応する。たいていは次の図のように二軸のみであらわす：

![](/images/norman-and-verganti/hill-climbing.png)

- 山登り法はデザインのような丘の形が事前にわかっていない場合に用いられる。ゆえに現在地からわずかな変分で何度か移動させてみて、高さが向上したものを選んでそこに移動することを、満足するまで繰り返す。この移動法はまさにHCDにおけるラピッドプロトタイピングとテストでやっていることだ。目隠しされた人が、ほうぼうの大地を触りながら丘の頂上を目指す状況を考えればよい。触ったなかで最も高いところに移動し、あらゆる方向が現地点よりも低くなる（頂上にたどりつく）まで繰り返すのだ。
- 山登り法は連続的な改善を保証するものの、ほかにより高い丘がデザイン空間のどこかにまだ存在するかを知る方法がないのだ。それゆえ山登り法は極大値`local maxima`に陥る。漸進的イノベーションはいま置かれている丘の最高点をめざす。ラディカルイノベーションは最も高い丘をめざす。HCDは漸進的イノベーションにのみむくのだ。

次のパラグラフの書き出しは感動的なので原文を引用する。

> Norman was bothered by his analysis and tried to find examples that refuted this conclusion; he failed. 

エッセイ，カナ⁉️😅

- ノーマンはこの分析にイラつき、反例を探した。そして失敗した。あらゆるラディカルイノベーションの例はデザインリサーチに欠け、人や、なんなら社会のニーズまでも丁寧に分析していなかったのだ。そもそもデザインリサーチが存在するずっと以前からラディカルなイノベーションはあった。しかしこんにちの、FacebookやTwitterのごときSNSといったラディカルイノベーションも「やってみたら面白そうだったから」はじまっているのだ。HCDによって生み出されたラディカルなイノベーションをノーマンは見つけられなかった。ノーマンはラディカルイノベーションはデザインリサーチや正式なニーズの調査なしに技術の変化によって生み出されると主張する。しかしひとたびラディカルイノベーションが開発されれば、HCDは魅力をのばし改善するにはとても良い方法だ。Google, Facebook, Twitterの改善や、自動車製造会社のゆっくりと、しかし連綿とした変更がいい例だ。

ラディカルイノベーションは技術の変化によって生み出される、というのはやや同語反復ぎみだがまあよいとして、その技術の変化が適応度地形のデザイン空間に含まれていないのがよくわからない。その技術の変化はどこから生み出されたのかといえば漸進的な変化によるもののはずだ。たしかに基礎的な技術（インターネットの技術など）の変化のレイヤーと、その応用法、とくにアイディア的なもの（Facebookなど）の変化のレイヤーはわけて考えることはできると思うが、それでいえばFacebookはなにもラディカルではない。Facebookが登場する何年も前からMyspaceが大きな成功を収めていたのだ。ごく小さな変更がその成功を大きく上回る成功を生み出したのだから、変化の幅という意味ではラディカルでもなんでもないのではないかというのが私の感想だ。

- この分析結果を発表する過程でノーマンはヴェルガンティの本[『デザイン・ドリブン・イノベーション』](https://www.amazon.co.jp/%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3%E3%83%BB%E3%83%89%E3%83%AA%E3%83%96%E3%83%B3%E3%83%BB%E3%82%A4%E3%83%8E%E3%83%99%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3-NextPublishing-%E3%83%AD%E3%83%99%E3%83%AB%E3%83%88%E3%83%BB%E3%83%99%E3%83%AB%E3%82%AC%E3%83%B3%E3%83%86%E3%82%A3-ebook/dp/B01MYNF37V/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2YU00CN00RS2O&keywords=%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3%E3%83%BB%E3%83%89%E3%83%AA%E3%83%96%E3%83%B3%E3%83%BB%E3%82%A4%E3%83%8E%E3%83%99%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3&qid=1660998563&sprefix=%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3+%E3%83%89%E3%83%AA%E3%83%96%E3%83%B3+%E3%82%A4%E3%83%8E%E3%83%99%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%2Caps%2C258&sr=8-1)をみつけ、似たようなことを主張していることを知った。
- ヴェルガンティはイノベーション・マネジメントの研究者であり、技術的イノベーションのマネジメントからデザインに移籍してきた。デザインを技術やマーケットのようなほかのイノベーションをうみだす要因と分かつデザインの定義を探し、`making sense of things`というデザインの定義に根ざす探求を続けている。KrippendorfやHeskettが説明するように。

> デザインという語の語源はラテンのde + signareからくる。なにかものをつくったり、サインでそれを区別したり、重要性を与えたり、他のものや所有者、ユーザー、もしくは神との関係を方向づけ`designate`したりすることを意味する。この原義から、Designとはmaking sense (of things)することだと言えるかもしれない。
> —Krippendorff, (1989)^[Klaus Krippendorff, “On the Essential Contexts of Artifacts or on the Proposition that ‘Design is Making Sense (of Things),’” Design Issues 5, no. 2 (1989): 9–38.]
> デザイン：意図的で理由付けられた環境の変更や作成であり、