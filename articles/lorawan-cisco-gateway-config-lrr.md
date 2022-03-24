---
title: "Cisco LoRaWAN Gateway: LRRのコンフィグ"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility, ThingPark]
published: true
created: "2022-03-17"
---

[Cisco LoRaWAN Gateway](https://www.cisco.com/c/ja_jp/products/collateral/se/internet-of-things/datasheet-c78-737307.html) (IXM)
で LRRをコンフィグしてみる。

LRRは、Actility製LoRaWAN Packet Forwarderのこと。
IXMのファームウエアは v2.3.0。
IXMのファームウエアのアップグレードは、[Cisco LoRaWAN Gatewayのファームウエアをアップグレードしてみる](/articles/lorawan-cisco-gateway-upgrade.md)を参考に。

**NSは TPCPを想定しているので注意。 TPWやTPEは対象外。**

## ThingParkのドキュメント

ThingParkは [Actility](https://www.actility.com/)の LoRaWANソリューションのブランド名。

全体の流れは下記を参照すること。
[Configuring the base station LRR ](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Configure-bs-lrr.htm)

IXMでの LRRのコンフィグの詳細は下記を参照すること。
Installing the LRR packageの[Cisco IXM](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Cisco-IXM.htm)

下記、ざっと目を通して流れをつかんでおくのがよい。
[Installing the ThingPark image on your base station](https://docs.thingpark.com/thingpark-enterprise/7.1/Content/BS-installation-guides/Install-ThingPark-image.htm)

以降は、上記ドキュメントを補完する目的で作った。参考程度に。

## LRRファームウエアをダウンロードする。

TPW,TPE,TPCP,パートナーなどからLRRのファームウエアを入手する。

TPCPからは、GWの登録画面でモデルを入力するとダウンロードできるようになる。
![](/images/lorawan-cisco-gateway-tpcp-010.png)

2022年3月17日時点、TPCPからダウンロードしたファイル名は
*`TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.tar.gz`* だった。

展開するとこんな感じ。

```
% tar ztvf TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.tar.gz
drwxr-xr-x  0 root   root        0 May 28  2021 ./
-rw-r--r--  0 root   root  1471990 May 28  2021 ./TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP_SAAS_2.6.53_v1.0_no-keygen.cpkg
-rwxr-xr-x  0 root   root 90621469 May 28  2021 ./ixm_mdm_i_k9-2.2.0.tar.gz
-rw-r--r--  0 root   root      451 May 28  2021 ./lrr-opk.pubkey
```

LRRのバージョンは、2.6.53だった。
**lrr-opk.pubkey**は、ファームウエアを検証するために使う公開鍵が入っている。

## LRRをインストールする。

IXMにログインして copyコマンドで
lrr-opk.pubkeyとファームウエアを IXMの flashへコピーする。

ここで、10.0.0.1はTFTPサーバ。

```
GW#copy tftp://10.0.0.1/lrr-opk.pubkey flash:
!

Download 451 bytes took 00:00:01 [hh:mm:ss]
```

```
GW#copy tftp://10.0.0.1/TP_Enterprise_BS_Image_cisco.CISCO_CIXM.1_any_TPCP
_SAAS_2.6.53_v1.0_no-keygen.cpkg flash:
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Download 1471990 bytes took 00:00:07 [hh:mm:ss]
```

次に、特権モードに入り、公開鍵とファームウエアをインストールする。

```
GW#configure terminal 
GW(config)#packet-forwarder install pubkey lrr-opk.pubkey
Installed successfully
```

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
LRRのコンフィグのために、コンテナにログインする必要がある。

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

## LRRからIXMを操作するためのコンフィグ

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

LRRのCursesベースのコンフィグツール。

経験的に端末の最小サイズは 80x35 くらい。
80x24だと表示がズレることがある。
大きい方がよい。

コンテナの中から起動する。

```
bash-3.2# su support 
```

ROLLBACKできる作業をしたら、かならずCOMMITする。

System connfigurationのNetworkからIPアドレスなどを変更すると接続が切れてしまう。
そうすると、コンテナのセッションが残ってしまい、
IXMのCLIからコンテナへログインができなくなるので注意が必要。

例えば、SUPLOGでdhcpとしてコンフィグして、
IOS側でスタティックにコンフィグしていると、
IOSのコンフィグが上書きされてしまい、
作業中に接続が切れたりしてトラブルの元になる。

SUPLOGに限った話ではないが、
オンサイトでコンソールケーブル経由でつないでから作業するのが安全でよい。

- 参考
    + [Connecting to SUPLOG](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Connect-to-SUPLOG.htm#_Ref83035792)
    + [Apply/Commit/Rollback mechanism](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Apply-commit-rollback-mechanism.htm#_Ref82616372)

## IPsecトンネルのための Publik Keyを作る。

SUPLOGを使う。

SUPLOGが起動したら
*Identifiers* → *Generate new key pair* から、
Enter 'yes' to confirm で **yes** と入力して *Confirm* を選択する。
![](/images/lorawan-cisco-gateway-tpcp-011.png)
![](/images/lorawan-cisco-gateway-tpcp-012.png)

keys generated と表示されたら **OK** を選択する。
![](/images/lorawan-cisco-gateway-tpcp-013.png)

*Get public key* を選択すると、
Public KeyがASCII-armored形式で表示されるので**コピー**しておく。
GWの登録の時に必要になる。

BEGIN PUBLIC KEYとEND PUBLIC KEYは含めなくてもよい。
![](/images/lorawan-cisco-gateway-tpcp-014.png)
![](/images/lorawan-cisco-gateway-tpcp-015.png)

- 参照: [Generating and retrieving the base station’s Public Key](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Generate-retrieve-bs-public-key.htm#_Ref82102684)
- 参考: [Configuring PKI](https://docs.thingpark.com/thingpark-enterprise/6.1/Content/BS-installation-guides/Configure-PKI.htm#_Ref82102686)

## アンテナをコンフィグする。

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
