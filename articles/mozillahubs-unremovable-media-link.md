---
title: "mozilla hubs: 消えない Media Link"
emoji: ""
type: "tech"
topics: [MozillaHubs]
published: false
---

created: 2022-03-16

blenderで作ったメッシュを使いながら、わりと大きな部屋を作るとたまに図のような Broken Media Link と表示された謎のオブジェクトが現れる。
![](https://storage.googleapis.com/zenn-user-upload/91eacf9f3889-20220316.png)
拡大するとこんな感じ。
![](https://storage.googleapis.com/zenn-user-upload/eb190f8fb9da-20220316.png)

blenderで、下記をチェックしてみる。

- メッシュのオリジンが面や辺にない。
  + メッシュから離れた位置にオリジンがあるオブジェクトを使うとこうなる場合がある。
  + **Object → Set Origin → Origin to Geometory** する。
- カーブを使っている。
  + テキストやカーブをメッシュに変換せずに glTFに変換して hubsに持ち込むとこうなる場合がある。
  + **Object → Convert → Mesh** する。
