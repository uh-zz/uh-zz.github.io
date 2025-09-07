---
title: 'Go Conference 2024参加レポ'
description: Go Conference 20224 に参加してきた感想です
author: ['@_uhzz_']
lastmod: 2024-06-11T00:00:00+09:00
publishDate: 2024-06-11T00:00:00+09:00
menu:
  sidebar:
    name: 'Go Conference 2024に参加レポ'
    identifier: go-conference-2024
    parent: look-back-on
    weight: 40
images:
  - ogp/go_conference_2024.png
tags: ['Go', 'ポエム']
categories: ['振り返り']
---

## はじめに

先日、オフラインで開催された Go Conference 2024 に参加してきました。

https://gocon.jp/2024/

Go Conference で登壇デビューできた感想と、リアタイできた発表のレポートをしていきます。

発表レポ(というか感想)は、[リアタイできた発表](#リアタイできた発表)をご参照ください。(しばらくポエムになります)

## 発表緊張した…

当日の発表スライドはこちらです

<iframe class="speakerdeck-iframe" frameborder="0" src="https://speakerdeck.com/player/3e521da6f3e84afab30dd21521cc361d" title="自動生成されたhttpエンドポイントごとにカスタムミドルウェアを挿入したい話" allowfullscreen="true" style="border: 0px; background: padding-box padding-box rgba(0, 0, 0, 0.1); margin: 0px; padding: 0px; border-radius: 6px; box-shadow: rgba(0, 0, 0, 0.2) 0px 5px 40px; width: 60%; height: auto; aspect-ratio: 560 / 315;" data-ratio="1.7777777777777777"></iframe>

https://gocon.jp/2024/sessions/21/

まずは、発表のためにフィードバックをいただいた皆さんほんとにありがとうございました！！

また、激励してくれた同僚＆元同僚にもほんとに感謝です :pray:

どうしても発表は緊張するものなのですが、スライドはみんなに見てもらって恥ずかしくないものを持ってきた、というのが心の支えになりました。

おかげで、当日は気持ちよく発表することができました。

会場からの反応もいただくことができて、発表後のエゴサが止まりませんでした笑

こちらもありがとうございます:wave:

{{< x user="ruby_engineer" id="1799386755399033217" >}}
{{< x user="mizushika1" id="1799386957409403298" >}}
{{< x user="rymiyamoto129" id="1799387433102172397" >}}
{{< x user="nakaryo79" id="1799387041991651671" >}}
{{< x user="k1LoW" id="1799387532213559766" >}}
{{< x user="yagi_eng" id="1799387629743669517" >}}
{{< x user="shino_nobishii" id="1799386948001522141" >}}
{{< x user="melpo_mel" id="1799386769361965549" >}}
{{< x user="y_taka_23" id="1799387632478392603" >}}

## 発表までの経緯

登壇のモチベーションとしては、対外的なプレゼンへの興味と、今回の発表内容に関する問題についてみんなどうやってるんだろと思ったからでした

CfP については 1on1 でもコツを相談していて、採択者に刺さりそう、みんな知りたいはず、という観点で書くといいよとアドバイスをもらっていたので、それに沿って作成しました。

発表されたときはほんとに嬉しかったです。

{{< x user="_uhzz_" id="1775899961248616838" >}}

それから、ゴールデンウィークに v0.1 となるスライドを作り、

{{< x user="_uhzz_" id="1788598021472796835" >}}

のびしーさんが開催してくれた発表練習会でフィードバックをもらったりしました。

{{< x user="shino_nobishii" id="1800016780565569582" >}}

この会でのフィードバックをもとに、v1.0 となるスライドを引っ提げて、社内でも発表練習会を開催しました。

社内の練習会では、自分でも引くほどグダってしまったのですが、それを踏まえた内容のレビューに加え、原稿作っていくといいかもなど、様々なアドバイスをしてくれました。

なにより、20 - 30 人ほどのエンジニアがオンライン＋オフラインで集まってくれたのはほんとに感動しました（足を向けて寝られません）

そんなこんながあり、さらなるフィードバックを盛り込んで v2.0 としたスライドが当日使ったものになります。

## オフラインで参加してみて

オフラインでの参加は初めてで、とにかく緊張の一日になると腹を括っていたのですが、受付け後すぐ元同僚の @k_omotani さんと @ShuheiTakeuchi さんにばったり会ってお話することができ、緊張が解けました

{{< x user="_uhzz_" id="1799250275074814463" >}}

その後はほんとに緊張することなく、LT の時間まで安心して発表を見れました。(リラックスのせいか、Post うるさくなってたかもです)

途中、各社ブースにも遊びに行って、ノベルティをいただいたりゲームしたり楽しみました

{{< x user="_uhzz_" id="1799299702128865664" >}}

コード片を見て、どの Go プロジェクトか当てるゲーム

スコアは置いといて、`go-sql-driver/mysql` まで当てたかったです笑

運営の方、優しく合いの手入れてくれてありがとうございました:+1:

また、懇親会では @convto さんとも久々にお話できて楽しかったです。

今回のもう１つの収穫は、Conference のテーマでもある「一期一会」のとおり、初めましての方とたくさんお話する機会ができたことでした！

お話できなかった方もいたのですが、X で反応してくれたりして、次会うときは挨拶できそう、という気持ちになれたのが大きいです

## リアタイできた発表

#### イテレータによって Go はどう変わるのか

https://audience.ahaslides.com/cl965inb88/review?lookback-tab=slides

イテレータは、Go1.23 でリリース予定とのこと

個人的には、シーケンシャルアクセスを言語側が提供してくれる、だけの理解だったのが、この発表のサンプルコードを見てて、早く試したい！という気持ちになりました。

`(*sql.Rows).Scan`の予想サンプルは、とくに刺さってこう書けたら気持ちいいだろうなという感想をもちました。
ループするたびに、イテレータがオブジェクトを返してくれる例

```go
seq := sql.RowsAll[*User](*db.QueryContext(ctx, `SELECT * FROM users LIMIT 100`))
for user, err := range seq {
  if err != nil {
    return err
  }
  fmt.Println(user)
}
```

#### Dive into gomock / Go Conference 2024

<iframe class="speakerdeck-iframe" frameborder="0" src="https://speakerdeck.com/player/5e9a720ed31f40849f97e56eb006cd0b" title="Dive into gomock / Go Conference 2024" allowfullscreen="true" style="border: 0px; background: padding-box padding-box rgba(0, 0, 0, 0.1); margin: 0px; padding: 0px; border-radius: 6px; box-shadow: rgba(0, 0, 0, 0.2) 0px 5px 40px; width: 60%; height: auto; aspect-ratio: 560 / 315;" data-ratio="1.7777777777777777"></iframe>

gomock は普段の開発でも使っているものの、まだまだ知らないメソッドや機能があることを知ることができた。

reflect mode は他ライブラリの interface からも対象にできるみたい。

組み込み関数の `Cond(fn func(x any)bool)` は任意の条件を渡せるらしくて、黒魔術みを感じることができた。

> 隠蔽された実装は限りなく魔法に近い
>
> いざというときに実装が読めないと、手が出なくなる
>
> 普段からライブラリの実装を読んでおく

いい話

#### Cleanup handling in Go / Go Conference 2024

<iframe class="speakerdeck-iframe" frameborder="0" src="https://speakerdeck.com/player/d5ccbb093fbd40588a8ead499c2f4ffc" title="Cleanup handling in Go / Go Conference 2024" allowfullscreen="true" style="border: 0px; background: padding-box padding-box rgba(0, 0, 0, 0.1); margin: 0px; padding: 0px; border-radius: 6px; box-shadow: rgba(0, 0, 0, 0.2) 0px 5px 40px; width: 60%; height: auto; aspect-ratio: 560 / 315;" data-ratio="1.7777777777777777"></iframe>

各方面のクリーンアップ関数の比較をしてくださっていて、改めて `defer` って賢いんだなと思った。

個人的にも、登録された関数が実行完了するまでは待ってくれ〜いうのが優先度高い自覚がある。

また、発表内で提案されていたパッケージをまたいだクリーンアップのアプローチは面白いな〜と聞いていた。

私の発表にも通ずる(と勝手に自負していますが)ところもあるからか、`context.Context`の Tips は聞いて楽しいです。

#### Go に const 型修飾を期待しなくてよい理由

https://docs.google.com/presentation/d/e/2PACX-1vSWVLveC-AdAoBebAVx3lU4C8bUSBN5_vx-1x_4AWgwddild-kkEqypIp0ox8gGA32SMPu3xchsqGDw/pub?start=false&loop=false&delayms=3000&slide=id.g2e3b19212c6_0_0

発表練習会でもご一緒して聞いていたのですが、const 修飾子の前知識がなくてもわかりやすく説明しててすごいなと思いました。

const 修飾を導入できても安全性は限定的になってしまう。

対して、Go はその安全性をプログラマの責務とする代わりに、セマンティクスがわかりやすく、データ競合は Race Detector でカバーするという整理がされている。

#### バイナリを眺めてわかる gob encoding の仕様と性質、適切な使い方 / understanding gob encoding

<iframe class="speakerdeck-iframe" frameborder="0" src="https://speakerdeck.com/player/86b4990309364a7aac14180fc2c7dad3" title="バイナリを眺めてわかる gob encoding の仕様と性質、適切な使い方 / understanding gob encoding" allowfullscreen="true" style="border: 0px; background: padding-box padding-box rgba(0, 0, 0, 0.1); margin: 0px; padding: 0px; border-radius: 6px; box-shadow: rgba(0, 0, 0, 0.2) 0px 5px 40px; width: 60%; height: auto; aspect-ratio: 560 / 315;" data-ratio="1.7777777777777777"></iframe>

gob に関しては転送効率が良い、という浅い知識しかなかったので、この発表はさらに踏み込んだ内容で面白かったです。

バイナリを読むパートに、0 と 1 のスライドを見ていて、眼力で `item` と読めますね、とスラスラ発表が進んでいくので、ふむふむと受け入れつつある自分に気づきました(?)

と、バイナリリーディングを経てみると、gob が型定義を最初に送って、あとは値となるデータを送るから、JSON よりも安く転送できる（ただし単体は JSON のが安い）というのが心で理解できました。

個人的に、gob に関しては用途に合わせてつかってね、絶対使えってことではないよ、という趣旨で発表されていたのがいいなと思いました。

#### 試してわかる Go Modules と Minimal Version Selection

https://docs.google.com/presentation/d/1X5dXShWTmjhQbXH7vXHnTLJ5Tca7QhU4Pq1YugGiIHs/edit#slide=id.p

デモと一緒に見ることで、Go Modules がどういう動作をするかについてこれも心で理解できた気がしました。

{{< x user="_uhzz_" id="1799344683673645115" >}}

{{< x user="k1LoW" id="1799345157386747978" >}}

MVS について、どこかで調べたことがあったので半信半疑だったが、雰囲気ロックファイルじゃねと思ってた自分がいることを自覚しました笑

スライドだけでなく、デモを挟むと実体験できる発表いいなと思いました。(いつかやってみたい)

## まとめ

オフラインいいなと思える機会でした。

もちろん、Goの濃縮されたキャッチアップの機会でもありますが、発表を通して会場と知識を共有している感じが、オフラインのいいところだなんだなと認識しました。

改めて、運営メンバーのみなさんありがとうございました :pray:

来年の Go Conference も楽しみにしてます :wave:

## 備考

表紙イラスト：[Loose Drawing](https://loosedrawing.com/)
