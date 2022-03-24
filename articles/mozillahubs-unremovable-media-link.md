---
title: "mozilla hubs: 消えない Media Link"
emoji: "🦝"
type: "tech"
topics: [MozillaHubs]
published: true
created: "2022-03-16"
---

blenderで作ったメッシュを使いながら、わりと大きな部屋を作るとたまに図のような Broken Media Link と表示された謎のオブジェクトが現れる。
![](/images/mozillahubs-unremovable-media-link-001.png)
拡大するとこんな感じ。
![](/images/mozillahubs-unremovable-media-link-002.png)

mozilla spokeでは消すことができない。

blenderで、下記をチェックしてみると消える場合がある。

- メッシュのオリジンが面や辺にない。
  + メッシュから離れた位置にオリジンがあるオブジェクトを使うとこうなる場合がある。
  + **Object → Set Origin → Origin to Geometory** する。
- カーブを使っている。
  + テキストやカーブをメッシュに変換せずに glTFに変換して hubsに持ち込むとこうなる場合がある。
  + **Object → Convert → Mesh** する。
