---
title: "LoRaWAN: Cisco LoRaWAN Gatewayのコンフィグ"
emoji: ""
type: "tech"
topics: [Cisco, LoRaWAN]
published: false
---

created: 2022-03-17

Cisco LoRaWAN Gateway (IXM) のコンフィグの例。

- 他のシスコ製品と同様に特権モードで設定する。
- sshのポート番号は変えた方がよい。
- ユーザ名は予測しづらい名前がよい。
- dhcpを使う場合は、*ip address dhcp* とする。
- *ip ssh admin-access* は必要な時だけ使う。

参考程度に。

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
username yosokusidurai password 8 ****
!
ip ssh port 53127
!
ntp server ip ntp.nict.jp
!
clock timezone Asia/Tokyo
```