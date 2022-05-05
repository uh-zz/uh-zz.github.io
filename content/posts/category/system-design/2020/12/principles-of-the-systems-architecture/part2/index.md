---
title: 'システム設計-part2-'
date: 2020-12-05T18:07:06+09:00
description:
menu:
  sidebar:
    name: システム設計-part2-
    identifier: principles-of-the-systems-architecture-part2
    parent: system-design
    weight: 10
tags: ['system-design', 'ddd']
categories: ['system-design', 'ddd']
---

## はじめに

バックエンドエンジニアの[ロードマップ][ロードマップ]に沿ってエンジニアとしての自己肯定感を養うシリーズです。

※[現場で役立つシステム設計の原則][現場で役立つシステム設計の原則]を元に記事を作成しています。

## 設計パターン

### 早期リターン

複雑になりがちな場合分けのロジックの見通しをよくしようというものです。

**ありがちな`if-else`をつなげた(例 1)**

```
Yen fee() {
  Yen result;

  if (isChild()) {
    result = chidFee();
  } else if (isSenior()) {
    result = seniorFee();
  } else {
    result = adultFee();
  }
  return result;
}
```

**さっきのコードからローカル変数を抜いて結果をすぐに`return`するようにした(例 2)**

```
Yen fee() {
    if (isChild()) {
    	return chidFee();
    } else if (isSenior()) {
    	return seniorFee();
    } else {
    	return adultFee();
    }
}
```

このように、値が決まるとすぐに`return`するやり方を**早期リターン**と言います。

### ガード節

**上記の例 2 から`else`を抜いた(例 3)**

```
Yen fee() {
    if (isChild()) return chidFee();
    if (isSenior()) return seniorFee();

    return adultFee();
}
```

`else`を抜いた早期リターンを**ガード節**と言います。非常にコンパクトですね。

単文の並びに変えることで、独立性が高くなります。また、単文同士が結合していない（疎結合)ので追加が容易です。

### 多態

それぞれのクラスを包括するようなクラス(インターフェース)を作ることで、使う側のクラス、メソッドはインターフェースさえ実装していれば、それがどのクラスでも気にする必要がありません。

**インターフェースを用意する(例 4)**

```
interface Fee {
  Yen yen();
  String label();
}

// 大人クラス
class AdultFee implements Fee {
  Yen yen() {
    return new Yen(1000);
  }

  String label() {
    return "大人";
  }
}

// 子クラス
class ChildFee implements Fee {
  Yen yen() {
   return new Yen(50)
  }

  String label() {
   return "子供";
  }
}
```

**使う側(例 5)**

```
class Reservation {
  List<Fee> fees; // 大人と子供のリスト

  Reservation() {
    fees = new ArrayList<Fee>();
  }

  // Feeはインターフェースなので、大人と子供の両方に使える
  void addFee(Fee fee) {
    fees.add(fee);
  }

  // 大人と子供の合計料金
  Yen feeTotal() {
    Yen total = new Yen();

    for (Fee fee : fees) {
      total.add(fee.yen());
    }
    reuturn total;
  }
}
```

インターフェースを使うことで、大人と子供の料金クラスをまとめて処理できました。

if 文を使わずに済むと見通しがよいですね。

このようにインターフェースを使用して、異なるクラスのオブジェクトを同じ型として扱う仕組みを**多態**と言います。

多態にすることで`Fee`のインターフェースを実装した別のクラス(シニアクラスなど)を追加しても`Reservation`クラスを変更することはありません。改修箇所が少なくなるのは大きなメリットです。

### 列挙型

多態は同じインターフェースを実装したクラスの一覧が分かりにくくなる場合があります`class`宣言の`implements`を見れば一目瞭然ですが、一つ一つ見ていくのに時間がかかります。

**列挙型**を使うことで区分(インターフェースのグループ)の一覧を作成することができます。

**列挙型を使って料金区分ごとのロジックを表現(例 6)**

```
enum FeeType {
  adult(new AdultFee());
  child(new ChidFee());
  senior(new SeniorFee());

  private Fee fee;

  private FeeTypt(Fee fee) {
    this.fee = fee;
  }

  Yen yen() {
    return fee.yen();
  }

  String label() {
    return fee.label();
  }
}
```

**使う側(例 7)**

```
Yen feeFor(Stirng feeTypeName) {
  FeeType feeType = FeeType.valueOf(feeTypeName);

  return feeType.yen();
}
```

`enum`クラスの`valueOf()`メソッドは`Map`の`get()`メソッドと同様に、if 文を使うことなく料金区分ごとのオブジェクトを取得できます。

このような振る舞いを持った列挙型(`enum`)を**区分オブジェクト**と言います。

## まとめ

前回同様、コードをスッキリさせる手法を見てきました。インターフェースを使うことで抽象的なメソッドを作ったり、列挙型を使って if 文をなるべく減らせることはコードの保守性、拡張性を高めてくれますね。

[ロードマップ]: https://github.com/kamranahmedse/developer-roadmap#back-end-roadmap
[現場で役立つシステム設計の原則]: https://www.amazon.co.jp/%E7%8F%BE%E5%A0%B4%E3%81%A7%E5%BD%B9%E7%AB%8B%E3%81%A4%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E8%A8%AD%E8%A8%88%E3%81%AE%E5%8E%9F%E5%89%87-%E5%A4%89%E6%9B%B4%E3%82%92%E6%A5%BD%E3%81%A7%E5%AE%89%E5%85%A8%E3%81%AB%E3%81%99%E3%82%8B%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91%E3%81%AE%E5%AE%9F%E8%B7%B5%E6%8A%80%E6%B3%95-%E5%A2%97%E7%94%B0-%E4%BA%A8/dp/477419087X
