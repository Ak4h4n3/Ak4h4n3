# システムメモ

今後のM.2 SSD換装での手順を記載する。

# 事前要件

鯖民からの許可,鯖民議会でのシステムメンテナンス時の強化を貰う。

セットアップ後のスペック :

```
Case    : Thermaltake Versa H17
CPU     : Core i5 10400F 6/12 2.90Ghz ~ 4.30Ghz
RAM     : DDR4 32GB 2133Mhz (16GB x2)
MB      : ASUS PRIME B460M-A 
SSD     : M.2 SSD 1TB(1000GB)
```

導入後のソフトウェア : 

```
OS               : Ubuntu 20.04 server
Java             : AdoptOpenJDK 11
Minecraft Server : Purpur 1.16.5
Minecraft Proxy  : Waterfall

SSH/SCP          : OpenSSH
Database         : MySQL / SQLite
Watchtool        : Zabbix
```

# ステップ 1 - 鯖機データのバックアップ

鯖機のデータを以降する際は、USBカードを利用し、cpで移動させること。
SCP接続の場合時間が掛かると言う事例が起こるため。

鯖機のデータを移行するために、以下のコマンドを実行する。

```
sudo cp /home/minecraft/ /media/...
```

# ステップ 2 - M.2 SSDに換装する

SSDを取り外し、M.2 SSDに換装してください。
M.2 SSD用の取り付けプラグは、ASUS PRIME B460-Aの箱の中に梱包されている。
そちらを使用し取り付けの方をする。
取り付けの際は、埃等の掃除や、pcケース内部の掃除等をしてください。(炎上防止)

# ステップ 3 - OSをセットアップする

まずは、Ubuntu Desktop 20.04.2.0 LTS のインストール用データ(ISOファイル)を下記サイトからダウンロードしてください。
[https://jp.ubuntu.com](https://jp.ubuntu.com/download)/download

## インストール用USBドライブの作成

インストール用データ(ISOファイル)を利用し、Rufusを用いてISOメディアの作成をしてください。

## インストール

こちらのサイトを参考にインストール作業の方行ってください。

[https://www.server-memo.net/ubuntu/ubuntu-server-2004_install.html#i](https://www.server-memo.net/ubuntu/ubuntu-server-2004_install.html#i)

注意点 : 現在USBの方からイーサーネットが通っていますが、USBの方から、マザボ直の方で接続+USBイーサーネット の二重の方にしてくれると助かります。

# ステップ 4 - ソフトウェアをセットアップする

まず、AdoptOpenJDK 11を導入する。
以下のコマンドを実行して導入。

```
sudo add-apt-repository ppa:rpardini/adoptopenjdk -y
sudo apt update
sudo apt install adoptopenjdk-11-installer -y
```

次に、Minecraft Server Softwareをセットアップする。
先程バックアップを取った、鯖機のデータを配置する。
ディレクトリは次の通りである。

```
/home/minecraft/shimasaba/
```

server.jarをPurpur.jarに置き換え、Purpur.jarをserver.jarに名前を書き換える。
(書き換えないとstart.sh側が認識しなく、鯖が起動しないので注意)

# ステップ 5 - サーバー監視ツールをセットアップする。

使用するソフトは、Zabbix , MySQL, Nginxを使用

下記からは今後記載する。 参考ページ : [https://harionote.net/2021/05/06/ubuntuで-nginx-zabbix-をサブドメインにインストールしてみた/](https://harionote.net/2021/05/06/ubuntu%E3%81%A7-nginx-zabbix-%E3%82%92%E3%82%B5%E3%83%96%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3%E3%81%AB%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F/)
