---
title: "LoRaWAN: Cisco LoRaWAN GWのバージョンアップ"
emoji: ""
type: "tech"
topics: [Cisco, LoRaWAN]
published: false
---

## ファームウエアの入手

created: 2022-03-17

下記から該当のファームウエアをダウンロードする。
https://www.cisco.com/c/en/us/support/all-products.html

下記の様に**lorawan**と入力すると候補が表示される。
![](https://storage.googleapis.com/zenn-user-upload/e8283209e72e-20220319.png)

2つ表示されるが、どちらも同じリンクになっている。
**Downloads**をクリックすると **Wireless Gateway for LoRaWAN 868MHz and 915MHz** のページに移動する。
![](https://storage.googleapis.com/zenn-user-upload/280cd0ff32c9-20220317.png)

2022年3月時点では最新版は 2.3.0 になっている。
ダウンロードアイコン（上図赤矢印）をクリックしてダウンロードする。

ダウンロードにはアカウントと権限が必要になる。
下記の表示が出たらログインしてからやり直す。
![](https://storage.googleapis.com/zenn-user-upload/8c64fbe0608f-20220317.png)

アカウントと権限については下記などを熟読する。
https://www.cisco.com/c/ja_jp/support/index.html

## 2.3.0 へのアップグレード

Cisco LoRaWAN GWのファームウエアを **v2.3.0**にアップグレードする。

まず、ファームウエアを GWにコピーする。

コピーせずにアップグレードする方法もあるが、
トラブルが起きた場合の切り分けが面倒なのでここでは一旦コピーする。

ファイルサーバ等からコピーするには FTPか TFTPを使う。

OS標準のTFTPのセットアップは意外と面倒なので、
[tftpy](https://github.com/msoulier/tftpy) が簡単でおすすめ。
ファームウエアが置いてあるディレクトリを指定して起動する。
下記は、ファームウエアが置いてあるディレクトリと
起動するディレクトリが同じ場合。

```
% tftpy_server.py -r .
```

ファイルサーバ側でtftpyを起動したら、
GWにログインして *enable*コマンドで特権モードにする。

下記、GWのIPアドレスを *10.0.0.7* としている。

```
% ssh 10.0.0.7
-------------------------------------------------

                  LPWA modem

               . : | : . : | : .
                   C I S C O

-------------------------------------------------
lora2017@10.0.0.7's password: 

Press RETURN to get started

su: using restricted shell

Press RETURN to get started

GW>enable 
Password: 
GW#
```

次に、*copy* コマンドを使ってコピーする。
下記は、
ファームウエアのファイル名は *ixm_mdm_i_k9-2.3.0.tar.gz* で、
ファイルサーバのIPアドレスが 10.0.0.1 の場合。

```
GW#copy tftp://10.0.0.1/ixm_mdm_i_k9-2.3.0.tar.gz flash:
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    : (省略)
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Download 91956702 bytes took 00:05:09 [hh:mm:ss]
GW#
```

環境にもよるが真横に置いても数分かかる。

下記は、ファイルサーバ側のtftpyのログ。

```
% tftpy_server.py -r .
[2022-03-17 19:24:25,924] Server requested on ip 0.0.0.0, port 69
[2022-03-17 19:24:25,924] Starting receive loop...
[2022-03-17 19:32:45,747] Setting tidport to 36093
[2022-03-17 19:32:45,747] Dropping unsupported option 'timeout'
[2022-03-17 19:32:45,747] requested file is in the server root - good
[2022-03-17 19:32:45,747] Opening file /home/admin/image/ixm_mdm_i_k9-2.3.0.tar.gz for reading
[2022-03-17 19:32:45,748] Currently handling these sessions:
[2022-03-17 19:32:45,748]     10.0.0.7:36093 <tftpy.TftpStates.TftpStateExpectACK object at 0x106c89ca0>
```

コピーが終わるとプロンプトが表示される。
*dir* コマンドで確認してみる。

```
GW#dir
Directory of flash:/
    : (省略)
  -rw-    91956702  Mar 17 2022 19:37:53  ixm_mdm_i_k9-2.3.0.tar.gz
```

最後に、*archive download-sw* コマンドを使ってアップグレードする。

```
GW#archive download-sw firmware /normal /save-reload flash:/ixm_mdm_i_k9-2.
3.0.tar.gz
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    : (省略)
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Copy 91956702 bytes took 00:00:01 [hh:mm:ss]
Validating archive...
Validation successful
Extracting images...
    : (省略)
133551+1 records out
68378221 bytes (65.2MB) copied, 5.394576 seconds, 12.1MB/s

System will reload in few seconds...
Reloading ...
```

*Reloading ...* と表示された後、接続が切れる。
切れた後、6-10分くらいで ping が通る様になる。
数分後、sshで入れることを確認する。

primary と backup のファームウエアを揃えるには、
もう一度、同じコマンドを使ってアップグレードする。
これを twice upgradeと呼ぶ。
普通は揃える。

```
GW#archive download-sw firmware /normal /save-reload flash:/ixm_mdm_i_k9-2.3.0.tar.gz
    : (省略)
```

再度、sshで入れることを確認して完了。

