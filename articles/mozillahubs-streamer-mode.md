---
title: "Mozilla Hubs: ストリーマーモードを使ってみる"
emoji: ""
type: "tech"
topics: [MozillaHubs, Blender, VR]
published: true
created: "2022-03-05"
---

ストリーマーモードを使うと、シーンのロビーにモデレータの視界が表示されるようになる。
使い方は、Room Settingsの[解説](https://hubs.mozilla.com/docs/hubs-room-settings.html#camera-mode)を参照する。

モデレータ（room ownerともいう）だけが設定できる。
右下のプルダウンメニューをクリックして**Enter Streamer Mode**を選択すると開始される。
ロビーの真ん中のタイトル (`hubs moz://a`)を消すには、`` ` ``(バッククォート)を押す。

参考までに、以前は Camera Modeと呼ばれていた。
https://twitter.com/MozillaHubs/status/1354121369022205953

さて、ロビーは初期状態だと真ん中に `hubs moz://a` と書かれたタイトルが表示される。
ブラウザなら `` ` ``(バッククォート)キーを押すと消せる。
が、ストリーミングを見る人が必ずしもキー入力できるとは限らないので激しく邪魔である。

キー入力なしで真ん中のタイトルを消したい。

botモードとかでやるのかな？
と思って、ざっとコード眺めたけどできなさそう。