---
title: "講義を配信する"
emoji: "📺"
type: "idea"
topics:
  - "obs"
  - "meet"
  - "camo"
published: true
published_at: "2020-12-14 18:00"
---

# 背景

2020-12-01から産業技術大学院大学（AIIT）で非常勤講師を勤めている。全講義がリモートでリアルタイムに配信される上に、それを10年間見返せるように録画をする必要がある。

最低要件は以下の通り。

 - Google Meetで講義のスライドと自分のウェブカムを配信する。
 - 同時に録画する。

Google Meetには録画機能があるが、それを利用できるのはG Suite利用者(Education含む)のみである。AIITではG Suite Educationを使っている模様だが、私は非常勤講師なので使えない。

追加で私が実現したかった要件は以下の通り。

- 高音質。リモートだと音質は画質よりも重要だと私は思う。外部のマイクを接続したい。
- 自分の声をリアルタイムでモニタリングしながら配信したい。
- 高画質のウェブカム。iMac付属のFaceTimeの画質はそこそこなので、改善したい。
- 高画質のスライド。まぁ普通にやればこちらはある程度高画質になりますが…
- 録画動画では背景を切り取ったウェブカムを載せたい。
- 無料のソフトウェアでやりくりしたい。

基本的にはゲーム配信と同じような要件なのだが、ゲーム配信は大半がWindows環境で行われる上、配信先はMeetではなくTwitchやYouTubeであり、サービス側の録画機能以外で録画することは珍しいためあまり参考にならない。これらをMacで実現したい。

私の環境は以下の通り。

- PC: Apple [iMac Early 2019 (27in)](https://support.apple.com/kb/SP790?locale=en_US)　プロセッサーとグラフィックカードを強めにしている。メモリは32GBを追加して40GBとしている。
![](https://storage.googleapis.com/zenn-user-upload/a3leuiwbnkvnw5b5829ah50f9ix0)
![](https://storage.googleapis.com/zenn-user-upload/wdxb3fixmhn8w622xi8ch9won8lm)

# 音

## ハードウェア

DEITY [D3](https://www.amazon.co.jp/%E3%80%901%E5%B9%B4%E4%BF%9D%E8%A8%BC%E3%80%91DEITY-%E3%83%96%E3%83%AD%E3%83%BC%E3%83%89%E3%82%AD%E3%83%A3%E3%82%B9%E3%83%88%E5%93%81%E8%B3%AA-%E3%83%9E%E3%82%A4%E3%82%AF%E3%83%AD%E3%83%9B%E3%83%B3-%E3%82%AA%E3%83%BC%E3%83%88%E3%82%AF%E3%82%A4%E3%83%83%E3%82%AF%E3%82%B9%E3%83%AA%E3%83%BC%E3%83%97-%E3%82%B7%E3%83%A5%E3%83%BC%E3%83%9E%E3%82%A6%E3%83%B3%E3%83%883-5mm%E3%83%9E%E3%82%A4%E3%82%AF%E5%87%BA%E5%8A%9B/dp/B07K59B3J5/ref=sr_1_3?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=deity+d3&qid=1607930701&sr=8-3) （¥13,000）を新たに購入し（たが、使い勝手を考えればUSB接続のマイク、たとえば[BlueのYetiのちっちゃいやつ](https://www.amazon.co.jp/Blue-Microphones-%E3%82%B3%E3%83%B3%E3%83%87%E3%83%B3%E3%82%B5%E3%83%BC-BM300SG-2%E5%B9%B4%E9%96%93%E3%83%A1%E3%83%BC%E3%82%AB%E3%83%BC%E4%BF%9D%E8%A8%BC/dp/B0822NRR6M/ref=sr_1_8?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=blue+yeti&qid=1607936759&sr=8-8)のほうがずっといいと思う。私はショットガンマイクのカッコが好きなのでこれにした）、既に家にあったAltemide [Tolomeo Table](https://www.artemide.com/en/subfamily/1849546/tolomeo-table) （¥30,000程度）に新たに購入したSmallRig[スーパーロッドクランプ](https://www.amazon.co.jp/gp/product/B00ZC4VQZC/ref=ppx_yo_dt_b_asin_title_o08_s00?ie=UTF8&psc=1)（1,300)で固定した。この固定は必須ではないが、ショットガンマイクをブームスタンドのように接続するために使った。このクランプはわりと重く、華奢なトロメオの関節を痛めそうだったので最終的には[ETSUMI 止めネジ](https://www.amazon.co.jp/gp/product/B00OUBKZ4G/ref=ppx_yo_dt_b_asin_title_o06_s00?ie=UTF8&psc=1)(¥740)を購入した。この製品の黒い円盤部分はトロメオの頭部の上の穴よりも少し大きい。そこを介して固定することができる。
![](https://storage.googleapis.com/zenn-user-upload/qldcn7bx7sg2ganzycsas14psjze)
![](https://storage.googleapis.com/zenn-user-upload/0k1uutuzxrhgurbvace08unnx4c3)
長さが足りないので[2mの4極延長コード](https://www.amazon.co.jp/gp/product/B078MCXMNH/ref=ppx_yo_dt_b_asin_title_o05_s00?ie=UTF8&psc=1)でiMacにつないだ。またD3は単4電池をバカバカ消費するので、家に転がっていたeneloopの単4電池を使った。

4極延長コードをiMacに接続するにはイヤホンジャックに挿入する必要がある。つまりPCの音が聞こえなくなる！学生がMeetに参加する音や動画の音、あらゆる通知音が聞こえなくなる！それは困るので、

1. イヤホンをUSBで接続する、もしくは
2. 4極プラグをUSBで接続する

必要がある。

まずは1.の方法の例。手持ちのGrace [SDAC-B USB](https://drop.com/buy/drop-grace-design-standard-dac-balanced)（¥18,000）を使ってイヤホンを接続した。DACである必要はまったくなく、どんな機材でもUSBからイヤホンのアウトプットが出ればよい。なんならワイヤレスイヤホンがあるならBluetoothでつなげばよい。スピーカーを使うとハウリングするので注意。
1.2.の方法のどちらでも、最もかんたんなのは、USB-Cポートがあれば[AppleのUSB-C - 3.5 mmヘッドフォンジャックアダプタ](https://www.amazon.co.jp/gp/product/B07LBTYDWQ/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)（￥1,036）を購入すること。このアダプタはちゃんと4極に対応している。インプットとしてもアウトプットとしても使える。BootcampでWindowsを使っている場合に生じる、iMacのイヤホンジャックが4極として認識されない問題（つまりイヤホンのアウトプットとしては使えるがマイクのインプットとしては使えない）もこれによって解決する。


## ソフトウェア

調べると色々と有料の魅力的なソフトウェアがあるが、ここではすべてMac付属や無料のソフトウェアでまかなっている。

### Audio MIDI Setup

次のように設定した。PCの音も拾いたいため、[Soundflower](https://github.com/mattingalls/Soundflower)をインストールしておく必要がある。Soundflowerの後進[Loopback](https://rogueamoeba.com/loopback/)はより直観的でよりよい選択肢だと思うが無料版には音質の制限がある。私のusecaseであればSoundflowerとAudio MIDI Setupで事足りる。

**Built-in Microphone**

![](https://storage.googleapis.com/zenn-user-upload/39lxg833tcmmcsw6ryxoxqj3ld1u)

Built-inとか言いつつ、どういうわけか外付けのDeity D3の音量である。マイクのレベルはここからは調整できないので、Soundの設定から変える。
![](https://storage.googleapis.com/zenn-user-upload/huawhdbjwjpqfavlvucbyfz1c0ih)

**SDAC-B**

![](https://storage.googleapis.com/zenn-user-upload/vkhnm2tq5jzz43n8hhz51sapg9xe)

音声のモニタリング用。

**Soundflower (2ch)**

![](https://storage.googleapis.com/zenn-user-upload/99jakglk8obhpxwkfop2t1zg454y)

PC内部の音を録画したり配信したりするためにある。

**Multi-Output Device**

左下の＋から追加。モニタリング用のデバイスとSoundflower(2ch)をオンにする。私の場合は以下のようになる。Drift Correctionは必要だった。

![](https://storage.googleapis.com/zenn-user-upload/d729zzohoxrr23anm8m88xrx6j7m)

### LadioCast(自分の声のモニタリング用)

![](https://storage.googleapis.com/zenn-user-upload/osbepyfac9d7owmec5zovs45vm40)

自分の声を聞くため。マイクの電池切れなどがわかる。またYouTubeビデオなどを講義で配信したいときに音量のバランスをとるためにも自分の声をモニタリングしていたほうがよい。タイピング音など雑音がどれくらい聞こえているかもわかる。

音量は色々調整する必要がある。

### Google Meet（配信用）

![](https://storage.googleapis.com/zenn-user-upload/0acusc2j8c2n5ng99ejou99cntul)

Microphoneを`Soundflower(2ch)`にするとPC内部の音が配信できるようになる。

### OBS (録画用）

Desktop Audioを追加する。デバイスはSoundflower(2ch)を選択する。
![](https://storage.googleapis.com/zenn-user-upload/wgdan5p6zzguf20ce9r56xeicqxz)
![](https://storage.googleapis.com/zenn-user-upload/5nf0239rdfcasf960e2rpcq7nt77)

LadioCastを使っていればDesktop Audioをオンにすれば声も録音されるため、それだけオンにしておけばあとは気にする必要はない。
![](https://storage.googleapis.com/zenn-user-upload/b7b4h41iuq3yevs0oundwq5pzczk)

# ウェブカム

## ハードウェア

iMac付属のウェブカムでも十分なのだが、せっかく手元にクソ高いスマートフォンがあるので、iPhone XSのカメラを利用したい。

問題はスマートフォンをどうやって理想的な角度で固定するかである。今回はiPhoneの箱を改造して即席でこんな器具を作った。4時間位かかった。
![](https://storage.googleapis.com/zenn-user-upload/jvrddhio2kfkkvqy35i7uh3wg5mw)
![](https://storage.googleapis.com/zenn-user-upload/5o73qfs5ij9ylzjlmai8dnjut64j)


## ソフトウェア

### iPhone側

[Camo](https://reincubate.com/camo/)をダウンロードし、言われるがままに設定する。EpocCamなどを試していたがこちらのほうが圧倒的によい。無料版で十分（気にならない程度のウォーターマークと機能制限）。そんなに熱くならない。

### iMac側

Camo Studioを言われるがままにダウンロードし、設定する。有線でiPhoneと接続すると次のように写る。

![](https://storage.googleapis.com/zenn-user-upload/m3slsjnyrhk6ncrwzbw2jl3t0g34)

めっちゃキレイ…

**リアルタイム配信用**

これをGoogle Meetでウェブカムに選べば良い。Chrome（なりなんなり、Meetを開いていたブラウザ）を再起動して、次のように設定する。
![](https://storage.googleapis.com/zenn-user-upload/1zhpnn5schwcwyajk0n0r242m2mc)
![](https://storage.googleapis.com/zenn-user-upload/ma92wiqee7w0k74as37r0128vbe4)
色々試している。今回は使わなかったが[OBS Virtual Camera](https://github.com/johnboiles/obs-mac-virtualcam)もかなり有用だと思う。Windows版もある。

**録画用**

バーチャルグリーンスクリーンのように背景を切り抜き、右下にウェブカム画像を載せて録画したい。しかしろくな切り抜き用ソフトがない。結局、今のところSkypeの設定画面を使うのが最もよさそうだった。

参考動画

@[youtube](nIiQNwiry-I)

Skypeをダウンロードし、サインインし、設定画面を開き、`Audio & Video`を開くと次のようになっている：
![](https://storage.googleapis.com/zenn-user-upload/hb4t1x3tgu87sdjbkkda7rn3dlg0)

ここでカメラを変更しようとしてもできない。Camo Studioを開き、右上の`Help &Integrations`を押し、Skypeを選ぶと、

![](https://storage.googleapis.com/zenn-user-upload/nu95z909kbqrdtrtgk2m6j8xmdlg)

（ここではAlready Installedになっているが）インテグレートすればCamoが追加される。

CameraをReincubate Camoに変更する：
![](https://storage.googleapis.com/zenn-user-upload/suwlrv4o984ou6an4199sljcmw61)

背景は緑1色`(r, g, b) = (0, 255, 0)`の画像を追加する。案外調べてもでてこないので、よければ以下を使ってください：

![](https://storage.googleapis.com/zenn-user-upload/0fssfduqlod1dvnwv0n6u7v8hse7)

このSkypeの設定画面は録画中常に開いている必要がある。そのため2枚目のディスプレイがあるととてもやりやすい。

OBSのSourceでDisplay Captureを追加する。
![](https://storage.googleapis.com/zenn-user-upload/bq6xvzj0x5x7ml43rpidqegz6o6g)

本当はWindow Captureを使いたいのだが、Macではラグが生じて厄介なので涙をのんで`Display Capture`＋`Crop`を使う。`Display`はSkypeの設定画面が写っているほうを選ぶ。

![](https://storage.googleapis.com/zenn-user-upload/wrmccff331z4dyxqv8lpyhgpn4va)

`Crop`はマニュアルにし、スカイプのカメラが写っている部分をうまいこと切り取る。この工程は毎回同じ場所にSkypeの設定画面を持っていかないといけないので、最も再現性が低い。私の場合はこんな感じ。

![](https://storage.googleapis.com/zenn-user-upload/auv5kygcv9x899xpfvyaulr802dv)

その`Display Capture`を右クリックして、`Filter`を開く。左下の＋から`Chroma Key`を追加すると透明になる。設定はデフォルトのままでいいと思う。

![](https://storage.googleapis.com/zenn-user-upload/45trcps9vd6lei7848ip2us23yyo)

あとは適当にリサイズしたり場所を変えれば良い。

![](https://storage.googleapis.com/zenn-user-upload/fxvv0cmsxrlxnm705p5qqaa0xb2q)

# スクリーンシェア

## ハードウェア

特になし。配信と録画に耐える強めのPCさえあればよい。

## ソフトウェア

### OBS

すべてこれでまかなえる。配信するほうのディスプレイの`Display Capture`を追加している…が、人によってはWindow Captureにしたりクロップしたりすればよい。Settingsは以下の通りにしている。

![](https://storage.googleapis.com/zenn-user-upload/2wgudah0us9vwg6q8wjqtt9bhvve)

オプションにあるのなら、たぶんハードウェアアクセラレーションは使ったほうがいい。

録画は`.mkv`で保存される。このままでは使いにくいのでOBSで`File>Remux recordings`で`MP4`にRemuxする。数GBあっても一瞬でRemuxできるので容量さえあれば気にすることではない。

![](https://storage.googleapis.com/zenn-user-upload/7b99mqix6s3qwmsppnxusoplc7sa)

ダウンスケールはふつうにBicubicでいい気もする。

# 配信と録画

共有するスクリーンとは別にコントロール用のディスプレイがあるとよい。私はいまのところ、iPad AirにSkypeの設定画面、とても小さくしたOBS、Meetのチャットを表示し、iMacにスライドを全画面表示したりウェブページを開いたりとスクリーンシェアしたいものを見せるようにしている。


## 解像度や表示サイズ

EasyResなどで解像度をいじったりもしたが、結局デフォルトに戻った。ただしウェブサイトを見せる場合はウェブサイト自体を拡大すると文字が読みやすくなる。Firefoxだと

![](https://storage.googleapis.com/zenn-user-upload/pqox5zntmyo7ycrp9eyc6gssofvm)

Chromeだと

![](https://storage.googleapis.com/zenn-user-upload/48k8btbijiut0akp6t3kttoik94a)

でデフォルトの表示サイズを変えられる。

## Tips

- Google Slidesの場合、左下に`Pointer`というのがあるのでこれを使うとポインターが追いやすくなる。楽しくなってポインターを動かしすぎますが…。
- スライドの右下か左下は重要なものを書かないでおけば、人間をそこにはめこんでもそこまで邪魔にはならない。
- 配信するにせよ録画するにせよ音量は難しい。何度か一人で録画したり一人でMeetに他のデバイスでログインするなどして微調整をしてからのほうがいい。
- 同様に、Meetに他のデバイスで参加すると動画の圧縮具合がわかる。文字が潰れていないかチェックできる。


## 録画したファイル

[YouTubeには1440pでアップロード]し、個人情報の多い部分をトリミングして公開している。大学にはHandbrakeで720pFastに落としたものを渡している。だいたい1コマで250MBくらいになる。
2020-12-14以前の動画は試行錯誤している。以降は↑のようにiPhoneを使っている（執筆![](https://storage.googleapis.com/zenn-user-upload/6lpxm4if73xxmiio0foytlfegp3e)時点での予定）。

# なんか足りないかも

思い出しつつなるべくすべて必要な設定は書いたつもりだが、書きこぼしがあるかもしれない。設定で詰まったら教えて下さい。追記・修正します。