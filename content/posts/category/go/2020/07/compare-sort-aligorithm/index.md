---
title: 'ソートアルゴリズムをGoで実装してみた'
date: 2020-07-05T18:07:06+09:00
description:
menu:
  sidebar:
    name: ソートアルゴリズム
    identifier: sort-algorithm-with-go
    parent: go
    weight: 10
images:
  - images/bio_1.jpg
tags: ['Go']
categories: ['Go']
---

## はじめに

バックエンドエンジニアの[ロードマップ][ロードマップ]に沿ってエンジニアとしての自己肯定感を養うシリーズです。

## マージソート

> マージソートは、ソートのアルゴリズムで、既に整列してある複数個の列を 1 個の列にマージする際に、小さいものから先に新しい列に並べれば、新しい列も整列されている、というボトムアップの分割統治法による。大きい列を多数の列に分割し、そのそれぞれをマージする作業は並列化できる。
>
> 出典：[wikipedia「マージソート」より引用][マージソート]

最悪の計算量が O(n log n) であるから少なくとも O(n^2)よりは速いんだろうなという印象（雑すぎるか）

以下「[ソートを極める！ 〜 なぜソートを学ぶのか 〜][ソートを極める！ 〜 なぜソートを学ぶのか 〜]」を元に実装してみた（なるべくソースを見ないで実装を試みたがマージする箇所は折れた、、）

```
package main

import (
"fmt"
"time"

    "github.com/uh-zz/traning/algorithm/shuffle"

)

func main() {
// ランダムな要素 n 個のスライス取得
input := shuffle.RandomIntList(n)

    inputLength := len(input)

// マージソート
MergeSort(&input, 0, inputLength)
}

// MergeSort マージソート
func MergeSort(input \*[]int, left, right int) {

    // 要素数1つの場合は抜ける
    if right-left == 1 {
    	return
    }

// 配列を２つに分けるインデックス
middle := left + (right-left)/2

// 配列左側
MergeSort(input, left, middle)
// 配列右側
MergeSort(input, middle, right)

    var buffer []int

    // 左側と右側をバッファにためる（右側反転）
    for index := left; index < middle; index++ {
    	buffer = append(buffer, (*input)[index])
    }
    for index := right - 1; index >= middle; index-- {
    	buffer = append(buffer, (*input)[index])
    }

    // マージする
    scopeLeft := 0
    scopeRight := len(buffer) - 1
    for index := left; index < right; index++ {

    	if buffer[scopeLeft] <= buffer[scopeRight] {
    		// 左側採用
    		(*input)[index] = buffer[scopeLeft]
    		scopeLeft++
    	} else {
    		// 右側採用
    		(*input)[index] = buffer[scopeRight]
    		scopeRight--
    	}
    }

}
```

これ考えたのぶっ飛んでるなあと思って Wikipedia 見てたら、考案者が[フォン・ノイマン][ノイマン]でやっぱりぶっ飛んでた（凄すぎ）

## 挿入ソート

> 挿入ソート（インサーションソート）は、ソートのアルゴリズムの一つ。整列してある配列に追加要素を適切な場所に挿入すること。平均計算時間・最悪計算時間がともに O(n2)と遅いが、アルゴリズムが単純で実装が容易なため、しばしば用いられる。安定な内部ソート。基本挿入法ともいう。in-place アルゴリズムであり、オンラインアルゴリズムである。
>
> 出典：[wikipedia「挿入ソート」より引用][挿入ソート]

これも「[ソートを極める！ 〜 なぜソートを学ぶのか 〜][ソートを極める！ 〜 なぜソートを学ぶのか 〜]」を元に実装してみた

```
package main

import (
"fmt"
"time"

    "github.com/uh-zz/traning/algorithm/shuffle"

)

func main() {

// ランダムな要素 n 個のスライス
input := shuffle.RandomIntList(n)

    // 挿入ソート
    for index := 1; index < len(input); index++ {

    	// 挿入したい値
    	insertValue := input[index]

    	// 挿入位置
    	point := index
    	for ; point > 0; point-- {
    		if input[point-1] > insertValue {
    			input[point] = input[point-1]
    		} else {
    			break
    		}
    	}
    	input[point] = insertValue
    }

}
```

こっちは実装もしやすく理解するのも難しくないという印象。最終的にはどちらもソートするというのにこの差は、、と思ってしまう。

## 速度比較

今回はソートアルゴリズムの実装がメインだったけど、２つあるなら比較するまでがアウトプットだろうなと感じたので比較します。

#### マシンスペック

- OS: macOS Catalina
- プロセッサ: 1.6 GHz(Core i5)
- メモリ: 16 GB

### 実施方法

要素数 n 個のスライスのソート時間を比較する。要素はそれぞれランダムになっている。

### 結果

| 要素数(n) | マージソート(O(n log n)(sec) | 挿入ソート(O(n^2)(sec) |
| --------: | ---------------------------: | ---------------------: |
|     1,000 |                     0.000357 |               0.000277 |
|    10,000 |                     0.005002 |               0.024778 |
|   100,000 |                     0.036705 |               1.524296 |
| 1,000,000 |                     0.341336 |                      - |

※挿入ソート（1,000,000）は 1 分以上かかったので省略

要素が倍になるほどマージソートの速さがわかりますね。

このページのソースは GitHub にもあります。

[ソートアルゴリズムの比較][ソートアルゴリズムの比較]

## 余談

アルゴリズムを１ヶ月で把握しようとしてましたがソートアルゴリズムの雰囲気を掴むだけでそれくらい時間かかりました、、

１つずつ精進ですね。。

[ロードマップ]: https://github.com/kamranahmedse/developer-roadmap#back-end-roadmap
[マージソート]: https://ja.wikipedia.org/wiki/%E3%83%9E%E3%83%BC%E3%82%B8%E3%82%BD%E3%83%BC%E3%83%88#:~:text=%E3%83%9E%E3%83%BC%E3%82%B8%E3%82%BD%E3%83%BC%E3%83%88%E3%81%AF%E3%80%81%E3%82%BD%E3%83%BC%E3%83%88%E3%81%AE,%E4%BD%9C%E6%A5%AD%E3%81%AF%E4%B8%A6%E5%88%97%E5%8C%96%E3%81%A7%E3%81%8D%E3%82%8B%E3%80%82
[挿入ソート]: https://ja.wikipedia.org/wiki/%E6%8C%BF%E5%85%A5%E3%82%BD%E3%83%BC%E3%83%88
[ノイマン]: https://ja.wikipedia.org/wiki/%E3%82%B8%E3%83%A7%E3%83%B3%E3%83%BB%E3%83%95%E3%82%A9%E3%83%B3%E3%83%BB%E3%83%8E%E3%82%A4%E3%83%9E%E3%83%B3
[ソートアルゴリズムの比較]: https://github.com/uh-zz/traning/tree/master/algorithm
[ソートを極める！ 〜 なぜソートを学ぶのか 〜]: https://qiita.com/drken/items/44c60118ab3703f7727f
