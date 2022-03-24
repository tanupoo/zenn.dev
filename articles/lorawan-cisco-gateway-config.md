---
title: "Cisco LoRaWAN Gatewayのコンフィグの例"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN]
published: false
created: "2022-03-17"
---

[Cisco LoRaWAN Gateway](https://www.cisco.com/c/ja_jp/products/collateral/se/internet-of-things/datasheet-c78-737307.html) (IXM) のコンフィグの例。

詳細は、[公式](https://www.cisco.com/c/ja_jp/support/routers/interface-module-lorawan-868mhz-915mhz/model.html)を参照すること。

下記、参考程度に。

- 他のシスコ製品と同様に特権モードで設定する。
- 特権ユーザ名は予測しづらい名前がよい。
    + 下記例ではマスクしてある。
- sshのポート番号は変えた方がよい。
- *ip ssh admin-access* は必要な時だけ使う。

例では、static IPv4アドレス *10.0.0.7* を使っている。
DHCPを使う場合は、*ip address dhcp* とする。
IPv6は未サポート。

`GW#` は、IXMのCLIのプロンプト。 hostnameを変えると変わる。

```
GW#show running-config
!
enable secret 8 ****
!
hostname GW
!
ip domain lookup
!
ip name-server 8.8.8.8
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
ip ssh port 53127
!
ntp server ip ntp.nict.jp
!
clock timezone Asia/Tokyo
```
