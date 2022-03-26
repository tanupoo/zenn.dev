---
title: "LoRaWAN: IXMでのLRRのトラブルシューティングの例"
emoji: "🦝"
type: "tech"
topics: [Cisco, LoRaWAN, Actility]
published: true
created: "2022-03-17"
---

Cisco LoRaWAN Gateway (IXM)で Actility Packet Forwarder(LRR)を動かしていて、何か問題があった時の解決のヒントを挙げてみる。
**自己責任**でどうそ。

## IXMでLRRのログを見る。

LRRのログを見るには、IXM-CLIから show packet-forwarderコマンドを使う。

```
GW#show packet-forwarder log name trace 250
```

別の方法として、コンテナにログインすると過去7日間のログを見ることができる。

ただし、UNIX系OSを使い慣れていない人はお勧めしない。**自己責任**で。

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

