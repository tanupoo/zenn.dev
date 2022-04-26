---
title: "LoRaWAN: Actility ThingPark Communityを使ってみる。"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility]
published: true
created: "2022-04-26"
---

Actility ThingPark Community Platform (TPCP) が2020年12月リリースされた。
最近、日本向けの無線プロファイルの動作確認ができたので、まとめてみる。

ThingParkは、超高機能で多くのキャリアでも採用されている
LoRaWANネットワークを運用するために必要な
ネットワーク・サーバを中心としたサービスのこと。

何がすごいって、これまでリリースされてきたLoRaWAN L2スペックの全てに
対応していて、国ごとの法規制に従ったプロファイルも多種サポートされている。
LoRaWANネットワークの状態を可視化するツールも備わっていて、
クラウド版もオンプレ版もある。
価格以外は全く文句ない。

## TPCPの制限

- 非営利目的で使用すること。
- ThingParkのサービスに無料でアクセスできる。
    + 無料なので止まっても泣かない。
    + SLAが必要な場合は、別のサービスを使う。
- クラウド版のみ。
- 有料サポートなし。
    + 有料サポートが必要な場合は、下記へ問い合わせる。
    + https://community.thingpark.org/index.php/support-options/
- フォーラムでのサポートがある。
    + https://support.thingpark.org/portal/en/community/thingpark-community
- デバイスは 50台まで接続できる。
- GWの上限は特に明記されていない。
    + ダッシュボードを見ると500となっている。
    + 500台のGWを個人で用意することは不可能。

## 次のステップ

- [TPCPにアカウントを作る](/tanupoo/articles/lorawan-tpcp-create-account)
- [Cisco LoRaWAN Gatewayを TPCPにつないでみる。](/tanupoo/articles/lorawan-cisco-gateway-tpcp)
- [センサーデバイスを TPCPにつないでみる。](lorawan-tpcp-adding-device)


