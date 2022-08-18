---
title: "R Markdownでのggplot2の日本語プロットをAGGをバックエンドに使いつつインライン表示する"
emoji: "📖"
type: "tech"
topics:
  - "r"
  - "rstudio"
  - "ragg"
  - "日本語フォント"
  - "rmarkdown"
published: true
published_at: "2021-09-29 14:48"
---

# やりたいこと

macOS(Big Sur 11.5.2)上の執筆時最新版RStudio(2021.09.0)でR Notebookを書いているとして、BackendのGraphics DeviceにAGGを指定しつつ、`ggplot2`パッケージを使って日本語を含む図を(plotペインではなく)インラインで表示したい。
![](https://storage.googleapis.com/zenn-user-upload/45885d95135a9f14b895b9b8.png)

# 問題

文字化けする。たとえば以下のようなシンプルなコードでも、

```r
library(tidyverse)
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot( aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text(family = "YuMincho") 
```
![](https://storage.googleapis.com/zenn-user-upload/031fffc016ac4cd118888684.png)

エラーは

```
Warning in grid.Call.graphics(C_text, as.graphicsAnnot(x$label), x$x, x$y,  :
  no font could be found for family "YuMincho"
```

とでる。

## geom_text()でフォントを指定しなければ当然動くが豆腐

```r
library(tidyverse)
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot( aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text() 
```

![](https://storage.googleapis.com/zenn-user-upload/bdd8362d00b7f9537d6253b7.png)

## fontregistererを使う

[github repo](https://github.com/Gedevan-Aleksizde/fontregisterer)を参考に、`{remotes}`を入れて、

```r
remotes::install_github("Gedevan-Aleksizde/fontregisterer", upgrade = "never")
```

してから`library(fontregisterer)`すると、
![](https://storage.googleapis.com/zenn-user-upload/8d18550aaa9bed6334a07f54.png)
少なくとも`geom_text()`部分はうまくいく。軸名などに日本語を使おうとすると、

```r
library(tidyverse)
library(fontregisterer)
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot(aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text(family = "YuMincho") +
  labs(title = "あああ", x = "いいい", y = "ううう")
```
![](https://storage.googleapis.com/zenn-user-upload/eeea4d644c0886f91ae3c6c2.png)
うまくいかないので、それは`theme_grey(base_family= "")`で指定するか、
![](https://storage.googleapis.com/zenn-user-upload/3efe0c5fec4a55a1fb998d18.png)

`theme_set()`で一括で登録する：

```r
library(tidyverse)
library(fontregisterer)
theme_set(theme(text = element_text(family = "YuGothic")))
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot(aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text(family = "YuMincho") +
  labs(title = "あああ", x = "いいい", y = "ううう")
```
ここで横着して、`geom_text()`にも適用されるやろと思うと豆腐が帰ってくる：
```r
library(tidyverse)
library(fontregisterer)
theme_set(theme(text = element_text(family = "YuGothic")))
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot(aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text() +
  labs(title = "あああ", x = "いいい", y = "ううう")
```
![](https://storage.googleapis.com/zenn-user-upload/15aa37f368fc83b0200d3b97.png)

これは[仕様らしいです](https://github.com/tidyverse/ggplot2/issues/1859)。

## library(ragg)をいれてもきちんと動く

が、正直なところ、差がわかるようなプロットではないため、raggがちゃんとあたっているのかあたっていないのかすら判断できない。なにか間違えてるかも：
```r
library(tidyverse)
library(fontregisterer)
library(ragg)
knitr::opts_chunk$set(dev = "ragg_png")
theme_set(theme(text = element_text(family = "YuGothic")))
iris |> 
  mutate(label = rep("花", nrow(iris))) |> 
  ggplot(aes(Sepal.Length, Sepal.Width, label = label)) +
  geom_text(family = "YuMincho") +
  labs(title = "あああ", x = "いいい", y = "ううう")
```

![](https://storage.googleapis.com/zenn-user-upload/50769aed7d5fa7c06917883f.png)

## おまけ、plot()もうまくいくようにできる

まだ`library(fontregisterer)`をロードしていない状態では次のようになる
```r
plot(1, main = "あああ", xlab="いいい")
```
![](https://storage.googleapis.com/zenn-user-upload/ce63cf5ea45c1e011d24a7d3.png)
しかし`par()`関数でYuGothicを指定してから、さらにこれら２行を同時に(cmd+shift+enterなどで)実行すると、エラーを吐き出しつつちょっとだけ日本語フォントを使える：

```r
par(family="YuGothic")
plot(1, main = "あああ", xlab="いいい")
```

![](https://storage.googleapis.com/zenn-user-upload/20e68b60981db7e930c790db.png)

しかしticks(値の文字)などは表示されていない。しかも、なぜかYuMinchoを指定すると軸名なども表示されなくなる：
![](https://storage.googleapis.com/zenn-user-upload/e8a30658bd3cb6d651f517c7.png)
これも`library(fontregisterer)`を使うとうまく表示されるようになる。ただしこれを用いてもこの２行を同時に走らせないといけないことにはかわりがない：
![](https://storage.googleapis.com/zenn-user-upload/beaccb271119944b2a771f88.png)
YuGothicでももちろんうまくいく：
![](https://storage.googleapis.com/zenn-user-upload/746902a7352efd5b5a1d5f23.png)

なんでだろう…。これらの症状は2021.12.0のDaily buildでも同様でした。

## おまけ、またpolygon edge not foundがでたら
RNotebookで、なぜか上記をすべてやっても動かなくなった場合。再起動しても治らない。まっさらなsessionで上記のミニマムなコードを.Rファイルで実行したら動くようになった。なぜ。

## PDFで書き出す

```r
ggsave(
  "./output/tmp.svg", 
  device = svglite::svglite, 
  unit = "mm",
  height = 30, 
  width = 70
)

rsvg::rsvg_pdf("output/tmp.svg", "output/plot.pdf")
```
# 謝辞

Tokyo.Rでill_identifiedさんに教えてもらってやっとできるようになった。ありがとうございます。
