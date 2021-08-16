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
Database         : MySQL
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

# ステップ 5 - データベースをセットアップする。

使用するソフトは MariaDBを使用し、データベースを建てる。

[参考元](https://zenn.dev/nemuki/articles/b5f97a570b852f)

apt-getから入れてしまうと少し前のパッケージがダウンロードされるので、curlからダウンロードする。
```
$ sudo apt install apt-transport-https
$ curl -LsS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash

[info] Repository file successfully written to /etc/apt/sources.list.d/mariadb.list
[info] Adding trusted package signing keys...
[info] Running apt-get update...
[info] Done adding trusted package signing keys
```
上記のコマンドを実行し、apt-transport-httpsをDLする。
``sudo apt update``をすると、MariaDBのパッケージを拾ってきているのがわかります。
```
$ sudo apt update
…
Get:X https://dlm.mariadb.com/repo/maxscale/latest/ubuntu focal InRelease 
Hit:X https://downloads.mariadb.com/MariaDB/mariadb-10.5/repo/ubuntu focal InRelease
Hit:X https://downloads.mariadb.com/Tools/ubuntu focal InRelease
```
本題のMariaDBパッケージをインストールします。
```
$ sudo apt install mariadb-server
```
MariaDBが動作しているかの確認方法
MariaDBはsystemdで動いているので、systemctlで確認できます。
```
$ systemctl status mariadb

● mariadb.service - MariaDB 10.5.8 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/mariadb.service.d
             └─migrated-from-my.cnf-settings.conf
     Active: active (running) since dddd YYYY-MM-DD hh:mm:ss JST; Xmin XXs ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
   Main PID: 12599 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 10 (limit: 4486)
     Memory: 69.5M
     CGroup: /system.slice/mariadb.service
             └─12599 /usr/sbin/mariadbd
```
MariaDBの初期設定
mysql_secure_installation を管理者権限で実行。

```
$ sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

# 現在のパスワードを入力(初期設定ではなにもないはずなのでそのままEnter)
Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

# Unix_socketを使うかどうか
Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

# rootパスワードを変えるかどうか, 今回はセキュリティのため、パスワードを使用します。
Change the root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

# 匿名ユーザーを削除するかどうか
Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

# rootのリモートログインを無効にするかどうか
Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

# テストデータベースを削除するかどうか
Remove test database and access to it? [Y/n] y
"/etc/mysql/mariadb.conf.d/50-server.cnf"
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

# 特権情報をリロードするかどうか
Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

MariaDB へのログイン
```
$ mariadb -u root -p
Enter passwork # 先程入力したrootパスワード
```

# ステップ 6 - サーバー監視ツールをセットアップする。

使用するソフトは、Zabbix , MariaDB, Nginxを使用

下記からは今後記載する。 参考ページ : [https://harionote.net/2021/05/06/ubuntuで-nginx-zabbix-をサブドメインにインストールしてみた/](https://harionote.net/2021/05/06/ubuntu%E3%81%A7-nginx-zabbix-%E3%82%92%E3%82%B5%E3%83%96%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3%E3%81%AB%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F/)
