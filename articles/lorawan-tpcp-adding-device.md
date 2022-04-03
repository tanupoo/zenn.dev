---
title: "LoRaWAN: センサーデバイスを TPCPにつないでみる。"
emoji: "🦝"
type: "tech"
topics: [LoRaWAN, Actility]
published: true
created: "2022-04-02"
---

Actility ThingPark Community Platform (TPCP) に、LoRaWANセンサーをつないでみる。

以降、LoRaWANセンサーをデバイスと表記する。

デバイスを登録するには、**Connection**が最低1つ必要になる。
Connectionは、デバイスから送られてきたデータを蓄積・可視化するサービスで、 アプリケーションサーバ(AS)と同じと思ってよい。

Connectionが1つも作られていない状態で、デバイスを登録しようとすると下記の様に警告が出る。
![](/images/lorawan-tpcp-adding-device-001.png)

## 前準備

TPCPにアカウントを作っておく。"[TPCPにアカウントを作る](/tanupoo/articles/lorawan-tpcp-create-account)"を参考に。
GWの設定は終わらせておく。
例えば、Cisco LoRaWAN Gatewayの設定は、"[IXMを TPCPにつないでみる](/tanupoo/articles/lorawan-cisco-gateway-tpcp)"を参照のこと。

下記ページに一連の流れが書かれてあるので一読するとよい。
https://community.thingpark.org/index.php/build-your-first-end-to-end-use-case/

## Connectionを作る。

Connectionをどこかで動かしているのが理想だけど、TPCPまでデータが上がってくるかどうかを確認するだけであればダミーを入力すればよい。

TPCPの[ダッシュボード](https://community.thingpark.io/tpe)の左のリストの中の **Connections**をクリックすると **Create**が現れるのでクリックする。
![](/images/lorawan-tpcp-adding-device-002.png)

下図、Connectionの種類を選択する画面で、`https://` をクリックする。
![](/images/lorawan-tpcp-adding-device-003.png)

Connectionの初期パラメータを入力する画面が表示されるので必要な項目を入力する。
![](/images/lorawan-tpcp-adding-device-004.png)

- Name: Connectionを識別する文字列を入力する。後から変えられる。
- URL: TPCPからの接続を受けるURLを入力する。後から変えられる。
    + テストなどの目的で、データを受け取る必要がなければ実在しなくてもよい。
    + 例えば、`https://not.yet.ready`
- Content Type: JSONを選択する。他に、XMLと JSON untypedが選択できる。
- Tunnel Interface Authentication Key: ConnectionがTPCPを識別するためのキー。そのままでよい。後から変えられる。
- Custom HTTP Headers: HTTPヘッダを指定できる。必要なければ空でよい。

入力が終わったら `CREATE` をクリックすると Connectionが作られる。
![](/images/lorawan-tpcp-adding-device-005.png)

## TPCPに デバイスを登録する。

ダッシュボードの左のリストの中の **Devices**をクリックすると **Create**が現れるのでクリックする。
![](/images/lorawan-tpcp-adding-device-006.png)

デバイスの選択画面が表示されるので、登録するデバイスを選択する。

ここでは 横河電機製Sushi Sensorシリーズの [XS770A](https://www.yokogawa.co.jp/solutions/solutions/iiot/maintenance/sushi-sensor-j/wireless-vibration-sushisensor/)を登録する。
デバイスのベンダー名や製品名が見つからない場合の登録方法は後述する。

**View More Manufactures**をクリックすると、登録してあるデバイスのベンダー名がリストされる。
![](/images/lorawan-tpcp-adding-device-007.png)

**Yokogawa**の凧マークを探してクリックする。
![](/images/lorawan-tpcp-adding-device-008.png)

デバイスの初期パラメータを入力する画面が表示されるので必要な項目を入力する。
![](/images/lorawan-tpcp-adding-device-010.png)

最初に Modelを選択するが、リージョンが **as923** になっているものを選択する。
大事なことなのでもう一度、リージョンが **as923** になっているものを選択する。
**as923** はリージョンを示していて、日本は AS923 に属している。
間違えると、恐い人がやってくるので注意する。
消されたくないので **Sushi Vibration sensor (XS770A) as923**を選択する。
![](/images/lorawan-tpcp-adding-device-009.png)

**Activation Mode**は、**Over-the-Air Activation (OTAA) with local Join Server**を選択する。
XS770Aは OTAAのみサポートしている。

参考までに、LoRaアライアンスは OTAAを推奨しており、Activation-by-Personalization (ABP)はサイトサーベイやトラブルシューティングでのみ使うことを推奨している。

その他のパラメータも注意して入力する。

- Name
    + デバイスを識別する文字列を入力する。
    + 後から変えられる。
- DevEUI [^1]
    + デバイス固有のIDを入力する。デバイスの裏や取説などに書いてある。
- JoinEUI (AppEUI)
    + デバイスに定義された値を入力する。8バイトの16進文字列。
- AppKey
    + デバイスに定義された値を入力する。16バイトの16進文字列。
- Connection
    + 上記で作った Connectionの名前を選択する。
    + 後から変えられる。

特に、DevEUI, Activation Mode, AppEUI, AppKeyは、後から変えられないので注意する。
つながらない場合は、これらの値が間違っている可能性が高い。

入力が終わったら `CREATE` をクリックするとデバイスが作られる。
![](/images/lorawan-tpcp-adding-device-011.png)

デバイスが正しく動いていれば、しばらくすると Connectionが INITIALIZATIONから **ACTIVE**に変わる。
また、Last Uplinkや Last Downlinkの時刻が表示され、**DevAddr**が付与される。
![](/images/lorawan-tpcp-adding-device-012.png)

数分たってもACTIVEにならない場合は、デバイスの電源を入れ直す。

デバイスの画面の下の方に、LoRaWANフレームの送受信状況が表示される。
問題があった場合の参考情報に使える。
![](/images/lorawan-tpcp-adding-device-013.png)

また、下の方にある `SHOW ALL`をクリックすると、WIRELESS LOGGERと呼ばれるWebベースのツールが起動する。
生々しくて画面を貼ることはできないが、これが最高に強力で、大抵のトラブルはこれで原因が分かる。

## デバイスがリストにない場合の登録方法。

デバイスの選択画面に、デバイスのベンダー名がリストにない場合や、ベンダー名があってもモデルのリストにデバイスが見つからない場合がある。

例えば、[NALTEC](http://naltec.jp/LPWA.html)や[Green House](https://www.green-house.co.jp/iot-wireless/lora-lorawan/)などは、ベンダーリストにない。

別の例では、[Globalsat]()はベンダーリストにあるが、 [LW-360HR](https://www.globalsat.com.tw/en/product-275344/LoRa-GPS-Tracker-Watch-with-Heart-Rate-Monitor-for-Lone-Worker-LW-360HR.html)は、モデルのリストにない。

この様な場合、ベンダー名として **LoRaWAN Generic**を選択する。
![](/images/lorawan-tpcp-adding-device-014.png)

そして、モデルは **LoRaWAN 1.0.4 - class A as923** を選択する。
![](/images/lorawan-tpcp-adding-device-015.png)

## ABPを使う場合の登録方法。

モデルによっては、ABPしかサポートしていない場合がある。また、どうしてもABPを使いたい場合がある。

デバイスの初期パラメータの **Activation Mode**で ABPを選択すると、セキュリティに関する入力項目が変わる。
ABPを選択した場合、DevEUI, NwkSKey, AppSKeyを入力する。

[^1]: LW-360HRは、LoRa MACと表記されている。