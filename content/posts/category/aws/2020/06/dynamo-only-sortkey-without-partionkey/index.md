---
title: 'DynamoDB のソートキーだけで絞り込みたいとき'
date: 2020-06-05T18:07:06+09:00
description:
menu:
  sidebar:
    name: DynamoDB のソートキーだけで絞り込みたいとき
    identifier: dynamo-only-sortkey-without-partionkey
    parent: aws
    weight: 10
images:
  - images/bio_1.jpg
tags: ['AWS', 'DynamoDB']
categories: ['AWS', 'DynamoDB']
---

## はじめに

Dynamo のテーブルに GSI(グローバルセカンダリインデックス)を貼ってハッシュキー＋ソートキーでクエリするパターンが通常の使い方かと思います。

ではソートキーを日付にしていた場合、同じ日付範囲のデータを一括で取得できる方法はありますでしょうか？

公式ドキュメントにはその辺の Tips なかったのですが、同僚から以下の記事を教えてもらいました。

[DynamoDB の設計力をあげたい][dynamodbの設計力をあげたい]

これの設計２を参考にしました。

全データ共通のダミー列を用意して、以下の GSI を作成します。

- パーティションキーはダミー列
- ソートキーに日付

これで同じ日付範囲の複数データを引っ張ってくることが可能になります。

確かに美しいと言えないかもしれませんが、機転の効いた方法だと思いました。

[dynamodbの設計力をあげたい]: https://www.ketancho.net/entry/2018/01/30/075500#%E8%A8%AD%E8%A8%88%EF%BC%92%E3%83%80%E3%83%9F%E3%83%BC%E5%88%97%E3%81%AB%E5%AF%BE%E3%81%97%E3%81%A6%E3%82%B0%E3%83%AD%E3%83%BC%E3%83%90%E3%83%AB%E3%82%BB%E3%82%AB%E3%83%B3%E3%83%80%E3%83%AA%E3%82%A4%E3%83%B3%E3%83%87%E3%83%83%E3%82%AF%E3%82%B9%E3%82%92%E8%B2%BC%E3%82%8B%E6%A1%88
