---
title: 'netパッケージで非推奨のTemporaryメソッドの扱いについて'
date: 2022-12-13T00:00:00+09:00
description:
menu:
  sidebar:
    name: kyash-advent-calendar
    identifier: kyash-advent-calendar
    parent: go
    weight: 10
tags: ['Go']
categories: ['Go']
---

## はじめに

こちらは[Kyash Advent Calendar 2022](https://adventar.org/calendars/7407) の 13 日目の記事です。

今年の 11 月に Kyash に入社しました！サーバサイドチームの[uehara](https://twitter.com/_uhzz_)です:wave:

今回は`net/http`パッケージの非推奨メソッドである`Temporary()`について、社のメンバーから知見を共有してもらったのでその話をします。

## net/http パッケージの 非推奨メソッド Temporary() について

`Temporary()`については、フューチャー社の記事にわかりやすくまとめられています。

https://future-architect.github.io/articles/20220203a/

上記の記事を踏まえて、ここでは非推奨になった経緯と対応について言及しようと思います。

サッと概要を話すと、`Temporary()`は`net.Error`インターフェースに定義されているメソッドで、一時的なエラーかどうか判定するために用意されています。
ただし、「一時的」というのがうまく定義されていないとの理由で、こちらのメソッドは Go1.18 で非推奨になりました。

> net.Error.Temporary has been deprecated.
> https://tip.golang.org/doc/go1.18

## Temporary()が非推奨になった経緯

前提として、`net.Error`インターフェースは、以下のように定義されています
※ソースは Go 1.19 です

```go
// An Error represents a network error.
type Error interface {
	error
	Timeout() bool // Is the error a timeout?

	// Deprecated: Temporary errors are not well-defined.
	// Most "temporary" errors are timeouts, and the few exceptions are surprising.
	// Do not use this method.
	Temporary() bool
}
```

非推奨になったときの issue を追ってみます

https://github.com/golang/go/issues/45729

issue によると、`Temporary()`を実装していて、かつ `true` を返している標準パッケージのメソッドは以下の２パターンになります。

### パターン 1: Timeout 系のエラーだけど、 `Temporary()` メソッドで `true` を返す

> - [context](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/context/context.go#167): context.DeadlineExceeded
> - [crypto/tls](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/crypto/tls/tls.go#105): All Dial timeouts.
> - [net](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/net/net.go#586): Various timeouts.
> - [net/http](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/net/http/transport.go#2501): Timeout when reading headers or bodies. (The error type is named httpError, but it is only used for timeouts.)
> - [net/http](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/net/http/h2_bundle.go#3398): Also, HTTP/2 timeout reading response headers.
> - [os](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/internal/poll/fd.go#51): os.ErrDeadlineExceeded (defined in internal/poll)

### パターン 2: Timeout 系ではないエラーで `Temporary()` が `true` を返す(本来こっちだけの想定)

> - [net](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/net/net.go#515): ECONNRESET and ECONNABORTED errors returned by accept().
> - [net](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/net/cgo_unix.go#37): EAI_AGAIN errors returned by getaddrinfo().
> - [syscall/syscall_plan9.go](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/syscall/syscall_plan9.go#71), [syscall/syscall_unix.go](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/syscall/syscall_unix.go#139), [syscall/syscall_js.go](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/syscall/syscall_js.go#78),[syscall/syscall_windows.go](https://go.googlesource.com/go/+/refs/tags/go1.16.3/src/syscall/syscall_windows.go#163):
>   EINTR, EMFILE, ENFILE, plus errors also considered timeouts: EAGAIN, EWOULDBLOCK, EBUSY, and ETIMEDOUT. (Some minor > variation between operating systems.)

つまり、実際に「一時的なエラー」とみなされるエラーは、以下のようなシステムコールエラーとのことです。

- ECONNRESET
- ECONNABORTED
- EAI_AGAIN
- EINTR
- EMFILE
- ENFILE
- EAGAIN
- EWOULDBLOCK
- EBUSY
- ETIMEDOUT

結論として、Timeout 系のエラーなのに`Temporary()`で`true`を返しているパターンを除くと、本来の`Temporary()`は少数のシステムコールエラーによるもの（`few exceptions are surprising`）である。しかし、前者をカウントしていることにより、`Temporary()`が`true`になるパターンが頻繁に発生してるように見えるため、`Temporary()`の定義が明確でないから非推奨にしたほうがいいんじゃね、とのことでした。

## linter でも非推奨であることを警告されます

社のメンバーから共有してもらうきっかけになった問題です。

以下のコードは、`net.Error`インターフェースを満たし、`Temporary()`をつかうサンプルです

```go:net.go
package main

import (
	"fmt"
	"net"
)

type MyNetError struct{}

func (m MyNetError) Error() string   { return "my net error" }
func (m MyNetError) Timeout() bool   { return false }
func (m MyNetError) Temporary() bool { return true }

var _ net.Error = &MyNetError{}

func myFunc() error { return MyNetError{} }

func main() {
	if ne, ok := myFunc().(net.Error); ok && ne.Temporary() {
		fmt.Println(ne.Error())
	}
}
```

このプログラムを linter でチェックすると、以下のように警告が出ます。
linter のランナーに、[golangci-lint](https://github.com/golangci/golangci-lint)をつかいます。

```
$ golangci-lint run
net.go:19:44: SA1019: ne.Temporary has been deprecated since Go 1.18 because it shouldn't be used: Temporary errors are not well-defined. Most "temporary" errors are timeouts, and the few exceptions are surprising. Do not use this method. (staticcheck)
        if ne, ok := myError().(net.Error); ok && ne.Temporary() {
```

この警告を回避するために、２つの方法が挙げられます。

## 回避方法１: `Temporary()` をつかわない

シンプルに`Temporary()`を消してしまうパターンです

```go
	if ne, ok := myError().(net.Error); ok {
		fmt.Println(ne.Error())
	}
```

ただし、標準パッケージではつかわれている箇所もあり、(限定的な)代替案についても[議論](https://groups.google.com/g/golang-nuts/c/-JcZzOkyqYI/m/xwaZzjCgAwAJ)されています。

## 回避方法 2: linter のチェックをしない

linter のチェックを行わないよう、ディレクティブを設定します。

### golangci-lint で linter のチェックをスキップする

https://golangci-lint.run/usage/false-positives/#nolint-directive

```go
	//nolint:staticcheck
	if ne, ok := myError().(net.Error); ok && ne.Temporary() {
		fmt.Println(ne.Error())
	}
```

### 直接 staticcheck を実行する

`//lint:ignore`ディレクティブをつかいます。

```go
	//lint:ignore SA1019 no problem, thanks
	if ne, ok := myError().(net.Error); ok && ne.Temporary() {
		fmt.Println(ne.Error())
	}
```

## さいごに

`Temporary()`で判定したいようなケースはなるべくさけて代替のエラーを見つけるのがよさそうですね

学びとしては、非推奨になったきっかけの issue を読んでみて、標準パッケージを読むことに対する抵抗が少しなくなったような気がしたことです:sweat_drops:
