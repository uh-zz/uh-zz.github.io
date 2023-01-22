---
title: '球面三角法による２点間の距離計算をGoで実装してみた'
date: 2020-07-06T18:07:06+09:00
description:
menu:
  sidebar:
    name: 球面三角法による２点間の距離計算
    identifier: spherical-trigonometry-with-go
    parent: go
    weight: 10
images:
  - images/bio_1.jpg
tags: ['Go']
categories: ['Go']
---

## はじめに

バックエンドエンジニアの[ロードマップ][ロードマップ]に沿ってエンジニアとしての自己肯定感を養うシリーズです。

地球上の２点間の距離計算ってアプリだと Google Map API を使えば完了！だと思いますが、どう計算してるかって気になりますよね？

今回は球面三角法を利用した地球上の２点間の距離計算を Go で実装します。（調べたらフツーにあるんですが）

## 球面三角法とは

その名の通り、三角関数を利用して球面上の辺や角の大きさを導出するものです。平面と球面とでの違いは辺の大きさが
球面では中心角によって表されることにあります。

よって、球面三角法を使用して算出した弧の長さ(中心角)と赤道の半径を乗算すると距離が求まります。

球面三角法の証明については、[球面三角形の定理][球面三角形の定理]を参考にしました！

("高校生に向けて"とある通り、非常にわかりやすかったです)

球面三角法の余弦定理を利用して実際に距離を算出する方法は[球面三角法の余弦定理][球面三角法の余弦定理]がわかりやすいです。

## 実装

実装したソースコードは Github でも確認できます。

[球面三角法を利用した２点間の距離計算][球面三角法を利用した２点間の距離計算]

```
package main

import "math"

// Coordinate 緯度経度
type Coordinate struct {
    Longitude float64
    Latitude float64
}

// EarthRadius 赤道半径
const EarthRadius = 6378140

// DistanceOnTheEarth 地球上の 2 点間の距離を出す（球面三角法）
func DistanceOnTheEarth(from, to Coordinate) float64 {

    fromLadLon := from.Longitude * math.Pi / 180
    fromLadLat := from.Latitude * math.Pi / 180

    toLadLon := to.Longitude * math.Pi / 180
    toLadLat := to.Latitude * math.Pi / 180

    alpha := math.Sin(fromLadLat)*math.Sin(toLadLat) +
    	math.Cos(fromLadLat)*math.Cos(toLadLat)*math.Cos(fromLadLon-toLadLon)

    arcAlpha := math.Acos(alpha)

    return arcAlpha * EarthRadius / 1000

}
```

## 動かしてみる

それでは実装した Go の関数を呼び出す簡単なアプリを動かしていきます。

※今回使用するアプリも Github 上の同じディレクトリにあるのでビルドすると使用できます。

アプリの挙動としては、

1. 2 点間の緯度経度情報を取得する（取得するために[外部 API][緯度経度]を利用します）
2. 1 で取得した２点の緯度経度情報を今回実装した距離計算の関数へ渡して算出する

比較するために[こちらのサイト][keisan]を利用します。

### 結果

|          場所           |   比較サイト |   今回のアプリ    |
| :---------------------: | -----------: | :---------------: |
| 西東京市 ~ 大阪市都島区 |   383.344422 | 383.3444215569602 |
| 札幌市厚別区　~ 沖縄市  | 2,231.318234 |  2231.3182342761  |

※地球上の半径 r は 6378.140km にしています。

。。。同じになってしまいました。。比較とはなんだったんだろう

まあよく捉えると、比較サイトのような便利計算サイトと同等?のものが作れたということでしょうか。

## 余談

久しぶりに証明を見たり計算を手で追っていく作業をしたので懐かしい気持ちになりました。

普段の業務でそこまで計算式を使わない分、こう自発的に調べて実装するのも楽しいと思いました。

[ロードマップ]: https://github.com/kamranahmedse/developer-roadmap#back-end-roadmap
[球面三角形の定理]: http://horibe.jp/PDFBOX/SphericalTriangles.pdf
[球面三角法の余弦定理]: https://qiita.com/port-development/items/eea3a0a225be47db0fd4#%E7%90%83%E9%9D%A2%E4%B8%89%E8%A7%92%E6%B3%95%E3%81%AE%E4%BD%99%E5%BC%A6%E5%AE%9A%E7%90%86
[球面三角法を利用した２点間の距離計算]: https://github.com/uh-zz/traning/tree/master/algorithm/distance
[keisan]: https://keisan.casio.jp/exec/system/1257670779
[緯度経度]: http://geoapi.heartrails.com/api.html
