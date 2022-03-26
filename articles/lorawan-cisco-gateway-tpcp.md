---
title: "LoRaWAN: IXMを TPCPにつないでみる。"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility]
published: true
created: "2022-03-17"
---

Actility ThingPark Community Platform (TPCP) がリリースされたので、Cisco LoRaWAN Gateway (IXM) を接続してみる。

## 前準備

TPCPにはアカウントを作っておく。"[TPCPにアカウントを作る](/tanupoo/articles/lorawan-tpcp-create-account)"を参考に。
IXMの設定は終わらせておく。
例えば、"[Cisco LoRaWAN Gatewayの設定の例](/tanupoo/articles/lorawan-cisco-gateway-config)"を参照のこと。
公式は、[こちら](https://www.cisco.com/c/ja_jp/support/routers/interface-module-lorawan/series.html)。

下記ページに一連の流れが書かれてある。
https://community.thingpark.org/index.php/build-your-first-end-to-end-use-case/

このページの**Step 4: Create the Gateway on Your Account**をクリックしてGWを登録する。

## TPCPに IXMを登録する。

ダッシュボードの左のリストの中の **Base Stations**をクリックすると **Create**が現れるのでクリックする。
![](/images/lorawan-cisco-gateway-tpcp-003.png)

**CREATING BASE STATION**という画面が表示されるので、右下の **View More Manufactures**をクリックする。
![](/images/lorawan-cisco-gateway-tpcp-004.png)

Ciscoをクリックする。
![](/images/lorawan-cisco-gateway-tpcp-005.png)

CiscoのGWを登録する画面が表示されるので、初めにモデルを選択する。
ここではスタンドアローン版を使うので **Macro V2.0 Standalone** を選択する。
![](/images/lorawan-cisco-gateway-tpcp-006.png)

続いて、
- **Name**: GWの名前を入力する。何でもよい。後から変えられる。
- **LRR-UUID**: IXM-CLIで *show packet-forwarder info*コマンドなどで表示される **LRRUUID** を入力する。取得方法は、"[Cisco LoRaWAN Gateway: LRRを動かしてみる。](/tanupoo/articles/lorawan-cisco-gateway-config-lrr)"を参照のこと。
- **RF Region**: *Japan 8-channels 20mW*を選択する。後から変えられる。
- **IPsec**: enabledを選択する。
![](/images/lorawan-cisco-gateway-tpcp-007.png)

- **Disable public key authentication**: チェックが**外れている**ことを確認する。
- **Public key**: SUPLOGの *Get public key* で取得した公開鍵を入力する。取得方法は、"[Cisco LoRaWAN Gateway: LRRを動かしてみる。](/tanupoo/articles/lorawan-cisco-gateway-config-lrr)"を参照のこと。
- **Mode**: GPSシグナルが取れる場所に置いてあれば、*Onboard GNSS position*を選択するとよい。後から変えられる。
![](/images/lorawan-cisco-gateway-tpcp-008.png)

**CREATE**をクリックすると登録が完了する。
![](/images/lorawan-cisco-gateway-tpcp-009.png)

## ネットワークを準備する。

IXMでLRRを動かす前に、IXMが接続されているネットワークを設定する。

- NTP, DNSを使えるようにする。
    + NTP: UDP 123
    + DNS: UDP 53

- IKEv2とNAT Traversalを使えるようにする。
    + IKEv2: UDP 500
    + NAT Traversal: UDP 4500
    + 下記2つのホストがIPsecトンネルの終端になる。
        + slrc1-poc.thingpark.com 52.47.178.109
        + slrc2-poc.thingpark.com 35.180.224.119

- IPsecを使わない場合
    + 動きそうだけど未検証。
    + FTP (TCP port 21)を通す必要がある。

## LRRを起動する。

LRRを起動する。

すでに起動していたら、止めて起動しなおす。

まず、起動しているか確認する。

```
GW#show packet-forwarder status
Status : Stopped
```

ここで Runningとなっていたら、下記の様に止める。

```
GW#configure terminal 
GW(config)#packet-forwarder stop
Stopped packet-forwarder
```

LRRを起動する。

```
GW(config)#packet-forwarder start
Started packet-forwarder
GW(config)#exit
```

起動したか確認する。

```
GW#show packet-forwarder status
Status : Running
```

IPsecトンネルが張られた後に、レジストレーションが走る。
NS側で Status が ACTIVEになれば成功。
数分かかる。

![](/images/lorawan-cisco-gateway-tpcp-016.png)

5分程度たってもACTIVEにならなければ、何か問題が起きている可能性がある。

トラブルシューティングは、"[Cisco LoRaWAN GatewayでのLRRのトラブルシューティングの例](/tanupoo/articles/lorawan-cisco-gateway-lrr-debug)"を参考に。

## LRR起動後のIXMの設定

IPsecの設定が追加されている。

```
GW#show running-config 
!
enable secret 8 ****
!
hostname GW
container log all
!
crypto ipsec profile primary
 ipaddr slrc1-poc.thingpark.com iketime 86400 keytime 82800 aes 128
 subnet 10.152.12.0/24
 rightid CN=slrc1_aws-eu-eco_actility-tpe-ope
 exit
ip domain lookup
!
ip name-server 8.8.8.8
!
crypto ipsec profile secondary
 ipaddr slrc2-poc.thingpark.com iketime 86400 keytime 82800 aes 128
 subnet 10.152.22.0/24
 rightid CN=slrc2_aws-eu-eco_actility-tpe-ope
 exit
!
interface FastEthernet 0/1
 description Ethernet
 ip address 10.0.0.7 255.255.255.0
 exit
!
ip default-gateway 10.0.0.1
!
username **** password 8 ****
!
username **** password 8 ****
!
ip ssh admin-access
ip ssh port 22
!
ntp server ip ntp.nict.jp
!
clock timezone Asia/Tokyo
ipsec lxc-restart-disable
ipsec cert install local enable
ipsec enable
```

