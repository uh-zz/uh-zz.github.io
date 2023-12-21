---
title: 'Futureパターンが使われているOSSを見てみた'
date: 2022-12-10T18:07:06+09:00
description:
menu:
  sidebar:
    name: Futureパターンが使われているOSSを見てみた
    identifier: qiita-advent-calendar
    parent: go
    weight: 10
tags: ['Go']
categories: ['Go']
---

## はじめに

[@uh-zz](https://twitter.com/_uhzz_)です！

この記事は、[Go Advent Calendar 2022](https://qiita.com/advent-calendar/2022/go)の 10 日目の記事になります！

今年は、個人的に色々なことに挑戦した年だったなあと振り返るとともに、去年のアドベントカレンダーからもう１年経つのか〜という気持ちです

この記事では、Go における Future パターンをご紹介できればと思います

## Future パターンとは

> あるメソッドを呼び出すとします。 もしもオブジェクトが、そのメソッドを実行できる状態なら、実行します。 でも、実行できない状態なら、将来実行できる状態になるまで待つようにしたいとします。 その時に使えるのがこの Future パターンです。 future は「未来」という意味です
>
> もう少し正確にお話しましょう。 単にあるクラスに 「実行できる状態になるまで待つ」 という機能を入れるわけではありません。 すでに存在しているクラスに一皮かぶせて、 「実行できる状態になるまで待てるような機能を追加する」 というのが Future パターンです。
>
> 出典: [結城浩, Future パターン, デザインパターン紹介](https://www.hyuki.com/dp/dpinfo.html#Future)

上記の参考記事内では、Java をつかったマルチスレッドプログラミングで Future パターンが実装されています。

引用箇所の説明がほぼすべてですが、イメージ図で補足するとこんな感じになります

```mermaid
flowchart LR
   呼び出し元 --> Futureメソッド -- 実行できるようになるまで待つ --> 処理するメソッド
```

呼び出し元と処理するメソッドの間に Future メソッドを挟むことで、Future メソッドがプロキシ的に働き、非同期的に処理するメソッドを実行できるようになっています。

## Go だとこんなかんじにかけるらしい

以下の記事で `Future/Promise`という説明書されています

https://ascii.jp/elem/000/001/486/1486902/

```go
package main

func readFile(path string) chan string {
  // ファイルを読み込み、その結果を返すFutureを返す
  promise := make(chan string)

	// readFile とは別のゴルーチンでファイルを読み出す
  go func() {
    content, err := os.ReadFile(path)
    if err != nil {
      fmt.Printf("read error %s\n", err.Error())
      close(promise)
    } else {
      // 約束を果たした
      promise <- string(content)
    }
  }()
  return promise
}
 
func printFunc(futureSource chan string) chan []string {
  // 文字列中の関数一覧を返すFutureを返す
  promise := make(chan []string)

	// printFunc とは別のゴルーチンで文字列操作する
  go func() {
    var result []string

		// futureSource は readFile で読みだしたファイルの中身です
		//
		// readFile(ファイル読み込み)が完了して、 futureSource(=promise) に
		// 中身が送信されるまでこの処理は実行されません
    for _, line := range strings.Split(<-futureSource, "\n") {
      if strings.HasPrefix(line, "func ") {
        result = append(result, line)
      }
    }
    // 約束を果たした
    promise <- result
  }()
  return promise
}

func main() {
  futureSource := readFile("future_promise.go")

	// 一見、 readFile が実行されたあとに、すぐ printFunc が実行されるように見えます
	// しかし、 printFunc の引数(futureSource)がチャネルになっているので、
	// futureSourceが値を受信するまで関数内で、futureSource を使うことができない
	//
	// よって関数内で実行待ちが発生します
  futureFuncs := printFunc(futureSource)

	// チャネル(futureFuncs)を受信するまでブロック
  fmt.Println(strings.Join(<-futureFuncs, "\n"))
}
```

※記事内にあるコードに、コメントを追記させていただきました。:pray:

Java で実現していた Future メソッドとは違い、Go ではチャネルをつかって実行待ちを表現できるようです。
main 関数だけ見ると、呼び出し側では同期的に見えますが、内部でチャネルによる非同期処理が行われています。

## 実際に使われているところを深ぼってみた

https://github.com/hashicorp/raft

`Error()`の実装はここ
https://github.com/hashicorp/raft/blob/6b4e32088e0bda22ea219fc89b0ee47f420e2b0b/future.go#L168

raft の apply だけを深堀りしてみるのもいいかもしれない

## おわりに

Future パターンを取り上げてみましたが、蓋を開けてみるとチャネルを使った並行プログラミングでよく目にするような処理に、”名前がついてたんだ〜！”と思う方もいるでしょう（私のことです）

パターンを知る → 使っている OSS を見にいく流れは体験としていいなと思ったので、来年も引き続きやっていきます:wave:
