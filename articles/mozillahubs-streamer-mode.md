---
title: "Mozilla Hubs: ストリーマーモードを使ってみる"
emoji: ""
type: "tech"
topics: [MozillaHubs, Blender, VR]
published: false
---

created: 2022-03-05

ロビーにモデレータの視界が表示されるようになる。
Room Settingsの[解説](https://hubs.mozilla.com/docs/hubs-room-settings.html#camera-mode)

モデレータ（room ownerともいう）だけが設定できる。
右下のプルダウンメニューをクリックして**Enter Streamer Mode**を選択すると開始される。
ロビーの真ん中のタイトル (`hubs moz://a`)を消すには、`` ` ``(バッククォート)を押す。

参考までに、以前は Camera Modeと呼ばれていた。
https://twitter.com/MozillaHubs/status/1354121369022205953

キー入力なしで真ん中のタイトルを消したい。

botモードとかでやるのかな？

と思って、ざっとコード眺めたけどできなさそう。