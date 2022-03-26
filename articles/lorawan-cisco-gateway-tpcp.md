---
title: "LoRaWAN: IXMを TPCPにつないでみる。"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility, ThingPark]
published: true
created: "2022-03-17"
---

Actility ThingPark Community Platform (TPCP) がリリースされたので、Cisco LoRaWAN Gateway (IXM) を接続してみる。

IXMの設定は終わらせておく。
例えば、"[Cisco LoRaWAN Gatewayの設定の例](/tanupoo/articles/lorawan-cisco-gateway-config)"を参照のこと。
公式は、[こちら](https://www.cisco.com/c/ja_jp/support/routers/interface-module-lorawan/series.html)。

## TPCPにアカウントを作る。

最初に https://community.thingpark.org/ にアクセスして、**Sign Up**をクリックしてアカウントを作る。
![](/images/lorawan-cisco-gateway-tpcp-001.png)

他のActilityプラットホームを使っている場合、そのアカウントを再利用できるらしい。試してない。
混ぜるとハマるかもしれないので別途作った。

ログインすると下記のページに入れる様になる。
![](/images/lorawan-cisco-gateway-tpcp-002.png)

興味ある人は、LoRaWANとActilityとThingParkについて知っておいて損はない。
ここでは、**Step 3: Build your first end-to-end use case** をクリックして先に進む。

その先のページの**Step 4: Create the Gateway on Your Account**をクリックしてGWを登録する。

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

## LRRのログについて

LRRのログを見るには、IOSから show packet-forwarderコマンドを使う。

```
GW#show packet-forwarder log name trace 250
```

コンテナにログインすると過去7日間のログを見ることができる。
UNIX系OSを使い慣れていない人はお勧めしない。自己責任で。

*$ROOTACT/var/log/lrr/TRACE.log* に LRRのログが記録される。

リアルタイムなログは下記コマンドで見ることができる。
終了は `Ctrl+C` を押す。

ターミナルエミュレータによっては `Ctrl+C`が効かない場合がある。
sttyで設定する。自己責任で。

```
bash-3.2# tail -f $ROOTACT/var/log/lrr/TRACE.log
```

過去のログは下記ファイルを参照する。
1日ごとにファイルが変わる。
ファイル形式は **TRACE_NN.log**
*NN*は 01から 07まで。
01が月曜日に該当する。

## Trouble Shooting: `ERROR: received LrrID==0 from twa lrcid=201 idxlrc=0`

下記の様なエラーが出続けて、NS側で見ると登録が完了しない。

```
01:14:37.211  (00880) [../main.c:4001] LAP LRC STARTED
01:14:37.211  (00880) [../main.c:4005] Get LrrID: from bootserver = 0, from twa = 1, lrrid=00000000
01:14:37.211  (00880) [../main.c:567] LRR send lrruid='005F86-024B069606FCF' to lrc='(null)' twa sz=284 idxlrc=1
01:14:37.463  (00880) [../main.c:4043] LAP RECV sz=28 szh=28 psz=0 lrrid=000000ca typ=32/32768(I) tms=0 rqtdelay=0
01:14:37.463  (00880) [../main.c:3560] get lrrid=00000000 from twa lrcuid=202 idxlrc=1
01:14:37.463  (00880) [../main.c:3593] ERROR: received LrrID==0 from twa lrcid=202 idxlrc=1 ! => retry later
01:14:41.543  (00880) [../main.c:6686] On timer failed to get lrrid from twa => lrrid '69606fcf' used
```

TWAは認証サーバ。
別のインスタンスに登録されている。

別のインスタンスの登録を削除してしばらく待つ。
ただし、12時間では改善しなかった。
TPCPから削除してやりなおしても改善しなかった。
1日経過するとつながるようになった。

## Trouble Shooting: packet-forwarder firmware is not installed

packet forwarderの状態が見れない。

```
GW#show packet-forwarder status
packet-forwarder firmware is not installed
```

インストールが失敗している。
ありがちなのが下記の様にキーワードを間違えている。

```
GW(config)#packet-forwarder install firmware TP_Enterprise_BS_Image_cisco.C
ISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.cpkg
packet-forwarder firmware validation failed
```

## Trouble Shooting: NSにつながらない。

20分たってもつながらない場合、下記のログが出力される。

```
16:07:43.393  (31526) [../main.c:6164] no LRC connection during more than 1200sec => revssh
```

LRCまでの接続性がない。
基本的なチェックをしてもつながらない場合は、IPsecの設定が間違っている可能性がある。

## Trouble Shooting: One session to container console is already open.

下記のエラーが出て、コンテナにつながらない。

```
GW#request shell container-console 
Enter System Password:  
One session to container console is already open.
Only one session to container console is allowed.
```

コンテナのセッションが残っている。

コンテナを再起動する。

```
GW(config)#container disable 
Reset Container's private network...
Container is stopped.
```

```
GW(config)#no container disable 
Container is started.
```

ただし、コンテナを再起動するとGPSアンテナのアクセスができなくなる場合がある。
その際はIXMを再起動する。

## Trouble Shooting: cannot open gps device

```
15:26:52.872  (03554) [../gps.c:202] ERROR: opening TTY port for GPS failed - Too many levels of symbolic links (/dev/ttyS1)
15:26:52.872  (03554) [../gps.c:180] GPS cannot open gps device '/dev/ttyS1'
```

GPSへのアクセスができていない。
IXMを再起動する。

## Trouble Shooting: RADIO cannot be started

```
15:27:18.453  (26629) [../lgw_x8.c:1282] BOARD1 RADIO cannot be started ret=-1 'Invalid version number for the top FPGA'
```

radio off になっている。

