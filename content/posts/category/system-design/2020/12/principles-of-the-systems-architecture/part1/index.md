---
title: 'システム設計-part1-'
date: 2020-12-05T18:07:06+09:00
description:
menu:
  sidebar:
    name: システム設計-part1-
    identifier: principles-of-the-systems-architecture-part1
    parent: system-design
    weight: 10
images:
  - images/bio_1.jpg
tags: ['system-design', 'ddd']
categories: ['system-design', 'ddd']
---

## はじめに

バックエンドエンジニアの[ロードマップ][ロードマップ]に沿ってエンジニアとしての自己肯定感を養うシリーズです。

※[現場で役立つシステム設計の原則][現場で役立つシステム設計の原則]を元に記事を作成しています。

## 設計パターン

### 値オブジェクト(Value Object)

Java で変数を扱うとき、int や String などで型定義しがちな初心者丸出しの実装をしていた私ですが、値オブジェクトを知ったとき眼からウロコでした。

値オブジェクトとは、汎用的な型(int や String)で型を定義するのではなく、**専用の**型(クラスやインターフェース)を定義します。

範囲の広い汎用的な型を使うのではなく、業務に合わせた値で制限するというものです。

値オブジェクトクラスはこんなかんじ

```
class Quantity {

    static final int MIN = 1;
    static final int MAX = 100;

    int value;

    Quantity(int value) {

    	if (value < MIN) {
    		throw new IllegalArgumentException("不正" + MIN + "未満");
    	}

    	if (value > MAX) {
    		throw new IllegalArgumentException("不正" + MAX + "超");
    	}

    	this.value = value;
    }

}
```

そして参照はこんなかんじ

```
Quantity quantity = new Quantity(50);
```

こうすることで Quantity 型は値の制限(0~100)付きの実装ができるので安全です。

制限を超えた値は不正と見なせるので業務ルールから外れることもありません。

#### 値オブジェクトは不変！！

変数の上書きは禁止しておきましょう。

もし上書きしそうであれば、新しいオブジェクトを作成しましょう。

オブジェクトは常に１つの値を持つように実装します。

このように不変なオブジェクトを設計する方法を**完全コンストラクタ**と言います。

今回の Quantity 型のような値オブジェクトのようにクラス名も業務で実際に使用している用語にするべきです。

そうすることで業務の理解とプログラムの設計を関連付ける手助けになり、変更のしやすいコードを維持することができます。

### コレクションオブジェクト

値オブジェクトは単一の型(int や String)のデータとそれに関連するロジックを１つのクラスにまとめるというコンセプトです。

コレクションオブジェクトも同様に、List/Set/Map といったコレクション型のデータとロジックを１つのクラスに閉じ込めようといったものです。

※オブジェクト指向ではデータとロジックを閉じ込めるというところがミソといっても過言ではないでしょう。

クラスはこんなかんじ

```
class Customers {
  List<Customer> cutomers;

  void add(Customer customer) {...}
  void removeIfExist(Customer customer) {...}

  int count() {...}

  Customers importantCustomers() {...}
}
```

Customer に関するロジックを寄せ集めたような感じです。

こうすることで変更箇所のこのクラスだけに閉じ込めることができます。(楽ちん)

使う側は Customer に関するメソッドを呼ぶだけでよくなるので、使う側のソースもスッキリするという
みんなハッピーになれるというわけです。

#### 参照をそのまま渡すべからず

値オブジェクト同様、「不変」であることが求められます。

もしも getList()が欲しくなる禁断症状があった時に有効な方法は、`同じ型のコレクションオブジェクト`を返すことです。

```
class Customers {
  List<Customer> customers;

  Customers add(Customer customer) {
    List<Customer> result = new ArrayList<>(customers);
    return new Customers(result.add(customer));
  }
}
```

こうすることで、既存の customers を渡すことなく新しいオブジェクトを生成して返します。または、`変更不可`にして返します。

```
class Customers {
  List<Customer> customers;

  List<Customer> asList() {
    return Collections.unmodifiableList(customers);
  }
}
```

意地で不変なオブジェクトを返すようにします。そうすることで変更による副作用の起きにくいプログラムを作ることに繋がります。

## まとめ

- 値オブジェクトもコレクションオブジェクトもどちらも「不変であれ」ということです。同じインスタンスを使い回そうとすればするほど、変更によるバグが出る可能性が高くなります。
- データとロジックは１つのクラスに閉じこめましょう。ロジックをまとめておくことで、使う側はメソッドを呼ぶだけで済むし、変更する場合はそのクラスだけを対象にすればよいわけです。人類の英知ですね。

## 備考

[現場で役立つシステム設計の原則][現場で役立つシステム設計の原則]

表紙イラスト：[Loose Drawing](https://loosedrawing.com/)

[ロードマップ]: https://github.com/kamranahmedse/developer-roadmap#back-end-roadmap
[現場で役立つシステム設計の原則]: https://www.amazon.co.jp/%E7%8F%BE%E5%A0%B4%E3%81%A7%E5%BD%B9%E7%AB%8B%E3%81%A4%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E8%A8%AD%E8%A8%88%E3%81%AE%E5%8E%9F%E5%89%87-%E5%A4%89%E6%9B%B4%E3%82%92%E6%A5%BD%E3%81%A7%E5%AE%89%E5%85%A8%E3%81%AB%E3%81%99%E3%82%8B%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91%E3%81%AE%E5%AE%9F%E8%B7%B5%E6%8A%80%E6%B3%95-%E5%A2%97%E7%94%B0-%E4%BA%A8/dp/477419087X
