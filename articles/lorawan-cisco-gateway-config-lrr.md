---
title: "Cisco LoRaWAN Gateway: LRRを動かしてみる。"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility]
published: true
created: "2022-03-17"
---

[Cisco LoRaWAN Gateway](https://www.cisco.com/c/ja_jp/products/collateral/se/internet-of-things/datasheet-c78-737307.html) (IXM)で LRRを動かしてみる。

LRRは、Actility製LoRaWAN Packet Forwarderのこと。
IXM自体の設定は、"[Cisco LoRaWAN Gatewayの設定の例](/tanupoo/articles/lorawan-cisco-gateway-config)"を参考に。

IXMのファームウエアは v2.3.0を使った。
IXMのファームウエアのアップグレードは、"[Cisco LoRaWAN Gatewayのファームウエアをアップグレードしてみる](/tanupoo/articles/lorawan-cisco-gateway-upgrade)"を参考に。

**LoRaWAN NSは TPCPを想定している。 TPWやTPEは対象外。**

## ThingParkのドキュメント

ThingParkは [Actility](https://www.actility.com/)の LoRaWANソリューションのブランド名。

全体の流れは、"[Configuring the base station LRR ](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Configure-bs-lrr.htm)"を参照すること。

IXMでの LRRの設定方法の詳細は、Installing the LRR packageの"[Cisco IXM](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Cisco-IXM.htm)"を参照すること。

"[Installing the ThingPark image on your base station](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Install-ThingPark-image.htm)"を、ざっと目を通して流れをつかんでおくのがよい。

以降は、上記ドキュメントを補完する目的で作った。参考程度に。

## LRRファームウエアをダウンロードする。

TPCPのGWの登録画面でGWのモデルを入力すると、LRRのファームウエアをダウンロードできるようになる。
![](/images/lorawan-cisco-gateway-tpcp-010.png)

2022年3月17日時点、TPCPからダウンロードしたファイル名は *`TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.tar.gz`* だった。

展開するとこんな感じ。

```
% tar ztvf TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.tar.gz
drwxr-xr-x  0 root   root        0 May 28  2021 ./
-rw-r--r--  0 root   root  1471990 May 28  2021 ./TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.cpkg
-rwxr-xr-x  0 root   root 90621469 May 28  2021 ./ixm_mdm_i_k9-2.2.0.tar.gz
-rw-r--r--  0 root   root      451 May 28  2021 ./lrr-opk.pubkey
```

LRRのバージョンは、2.6.53だった。
**lrr-opk.pubkey**には、ファームウエアを検証するために使う公開鍵が入っている。

## LRRをインストールする。

IXMにログインして *copy*コマンドで lrr-opk.pubkeyとファームウエアを IXMの flashへコピーする。

下記、*10.0.0.1* はTFTPサーバのIPアドレス。

まず、lrr-opk.pubkeyをコピーする。

```
GW#copy tftp://10.0.0.1/lrr-opk.pubkey flash:
!

Download 451 bytes took 00:00:01 [hh:mm:ss]
```

次に、ファームウエアをコピーする。順番は関係ない。

```
GW#copy tftp://10.0.0.1/TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP
_SAAS_2.6.53_v1.0_no-keygen.cpkg flash:
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Download 1471990 bytes took 00:00:07 [hh:mm:ss]
```

そして、IXMの特権モードに入り、公開鍵とファームウエアをインストールする。

まず、特権モードに入る。

```
GW#configure terminal 
```

そして、公開鍵をインストールする。

```
GW(config)#packet-forwarder install pubkey lrr-opk.pubkey
Installed successfully
```

次に、ファームウエアをインストールする。

```
GW(config)#packet-forwarder install firmware flash:TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.cpkg
packet-forwarder firmware validation successful 
Starting new pktfwd-firmware...
Installed successfully
GW(config)#exit
```

インストール直後のLRRの状態。

```
GW#show packet-forwarder status
Status : Stopped
```

*show packet-forwarder info*コマンドで、詳細を見ることができる。

```
GW#show packet-forwarder info
PublicKeyStatus : Installed
FirmwareStatus : Installed
PacketFwdVersion : 2.6.53
LRRID : 
LRRUUID : 005xxx-024xxxxxx6FCF
PartnerID : ----
```

ここで **LRRUUID**をメモっておく。
GWの登録の時に必要になる。

## コンテナへのログイン方法

LRRは、IXMの中のLXCコンテナで動作する。
LRRの設定のために、コンテナにログインする必要がある。

コンテナにログインするには、IXMのIOSのプロンプトから下記の様にする。
IXMの特権ユーザのパスワードを使う。

```
GW#request shell container-console 
Enter System Password:  

Connected to tty 0
Type <Ctrl+a q> to exit the console, <Ctrl+a Ctrl+a> to enter Ctrl+a itself
bash-3.2#
```

`bash-3.2#` はコンテナのプロンプト。

## LRRからIXMを操作するための設定

LRR側からIXMを操作するための専用ユーザを作る。
IXMの特権ユーザと同じでも動作するが、ここでは専用ユーザを作る。

```
GW#configure terminal 
GW(config)#username ****hoge password ****puyo
chpasswd: password for '****hoge' changed
```

コンテナにログインして **credential.txt**を編集する。
ここでは *vi*コマンドを使う。

```
bash-3.2# vi $ROOTACT/usr/etc/lrr/credentials.txt 
```

3行書く。上から enableパスワード, LRR管理用ユーザ名, そのパスワード。

    ENABLE_PASSWORD
    LRR_ADMIN_USERNAME
    LRR_ADMIN_PASSWORD

1行ずつechoしていっても可。

```
bash-3.2# echo "ENABLE_PASSWORD" > $ROOTACT/usr/etc/lrr/credentials.txt 
bash-3.2# echo "LRR_ADMIN_USERNAME" >> $ROOTACT/usr/etc/lrr/credentials.txt 
bash-3.2# echo "LRR_ADMIN_PASSWORD" >> $ROOTACT/usr/etc/lrr/credentials.txt 
```

## SUPLOGについて

LRRのCursesベースの設定ツール。

経験的に端末の最小サイズは 80x35 くらい。
80x24だと表示がズレることがある。
大きい方がよい。

起動は、コンテナの中から行う。

```
bash-3.2# su support 
```

ROLLBACKできる作業をしたら、かならずCOMMITする。

System connfigurationのNetworkからIPアドレスなどを変更すると接続が切れてしまう。
そうすると、コンテナのセッションが残ってしまい、IXMのCLIからコンテナへログインができなくなるので注意が必要。

例えば、SUPLOGでdhcpとして設定して、IOS側でスタティックに設定していると、IOSの設定が上書きされてしまい、作業中に接続が切れたりしてトラブルの元になる。

SUPLOGに限った話ではないが、オンサイトでコンソールケーブル経由でつないでから作業するのが安全でよい。

- 参考
    + [Connecting to SUPLOG](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Connect-to-SUPLOG.htm#_Ref83035792)
    + [Apply/Commit/Rollback mechanism](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Apply-commit-rollback-mechanism.htm#_Ref82616372)

## IPsecトンネルのための公開鍵ペアを作る。

SUPLOGを使う。

SUPLOGが起動したら *Identifiers* → *Generate new key pair* から、Enter 'yes' to confirm で **yes** と入力して *Confirm* を選択する。
![](/images/lorawan-cisco-gateway-tpcp-011.png)
![](/images/lorawan-cisco-gateway-tpcp-012.png)

keys generated と表示されたら **OK** を選択する。
![](/images/lorawan-cisco-gateway-tpcp-013.png)

*Get public key* を選択すると、Public KeyがASCII-armored形式で表示されるので**コピー**しておく。
GWの登録の時に必要になる。

BEGIN PUBLIC KEYとEND PUBLIC KEYは含めなくてもよい。
![](/images/lorawan-cisco-gateway-tpcp-014.png)
![](/images/lorawan-cisco-gateway-tpcp-015.png)

- 参照: [Generating and retrieving the base station’s Public Key](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Generate-retrieve-bs-public-key.htm#_Ref82102684)
- 参考: [Configuring PKI](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Configure-PKI.htm#_Ref82102686)

## アンテナのパラメータを設定する。

Cisco LoRaWAN GWのアンテナは2種類ある。

SUPLOGの *Radio configuration* → *Set Power Transmission Adjustment* から下記の値を入れる。

- ANT-WPAN-OM-OUT-Nの場合
> Antenna number: 0
> Antenna gain: 4.0
> Cable attenuation: 0.5

- ANT-LPWA-DB-O-N-5の場合
> Antenna number: 0
> Antenna gain: 5.4
> Cable attenuation: 1.2
