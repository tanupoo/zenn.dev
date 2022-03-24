---
title: "Godot: Input Mapの Physical Keyと Keyの違い"
emoji: "🦝"
type: "tech"
topics: [Godot]
published: true
created: "2022-02-21"
---

Project Settings → Input Map で GDScriptから参照するキーの名前を定義できる。

プラスボタンを押した時に出てくる **Physical Key** と **Key** の違いについて、
Godot 3.4 is released with major features and UX polish の
[Improved input handling](https://godotengine.org/article/godot-3-4-is-released#input)によると、
Physical Keyは、キーボードの物理的な位置を指定するらしい。

例えば、QWERTY配列の`W`キーと AZERTY配列の `Z`キーの物理的なキーの位置は同じなので、
Input Mapで新たなアクションを定義する時に、Physical Keyを選択してからQWERTY配列キーボードを使って`W`を定義しておくと、AZERTY配列キーボードでも同じ場所のキーが割り当てられる。

…らしい。

