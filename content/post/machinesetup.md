---
title: 計算機のセットアップ手順書
date: 2021-02-22
categories:
  - "Articles"
tags:
  - "PC"
---

最近PCを新しくしてセットアップに結構手間取ったので備忘録として．
<!--more-->


## 環境
- OS: Windows 10 Home
- MB: ASRock B550 Pro4
- CPU: AMD Ryzen 5 3600
- GPU: MSI GTX 1650
- RAM: Crucial 16GB DDR4-2666 UDIMM x2

## 必要なソフトウェアのインストール
環境構築には[Scoop](https://github.com/lukesampson/scoop)を使用する．
ScoopとはMacのhomebrewやLinuxのaptに相当するWindows用のパッケージマネージャである．類似のプログラムとして[Chocolatey](https://chocolatey.org/)があるが，Scoopは管理者権限を必要としないことや，自作のスクリプトを簡易に作成可能であるというメリットがある．反面，Adobe製品のような商用ソフトについては別個にインストールする必要がある．

Powershellを立ち上げ，以下のコマンドを実行しインストール．
```ps1
# スクリプトの実行を許可する
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
# Scoopのインストール
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```
基本的なコマンドを列挙する．
```ps1
# アプリケーションを検索
scoop search $appname
# インストール
scoop install appname
# アンインストール
scoop uninstall $appname
```

## Python環境の構築
pipとvenvをインストール．
- numpy: 多次元配列を扱える数値計算ツール
- scipy: 統計，科学計算
- pandas: データの整形
- matplotlib: データの可視化
- jupyterlab: アドホック分析ツール
- numba: 事前にコンパイルして計算を高速化

2020年1月時点で最新版のPython3.9系はnumbaに対応していないので3.8系を指定してインストールする．
```ps1
$ scoop install python@3.8.8
```
必要なライブラリをインストール．
```ps1
$ python -m venv ${env_name}
$ ${env_name}/Scripts/Activate.ps1
(${env_name})$ pip install numpy scipy pandas matplotlib jupyterlab numba
```

## Rclone
[Rclone](https://rclone.org/)はGo言語で記述されたオープンソースのクラウドストレージの管理用プログラムである．RcloneはDropboxやGoogle Drive，OneDriveといった主要なクラウドストレージサービスのほとんどを網羅し，Unixで用いられるrsyncやcp，mv等のコマンド群と同等の機能を有している．WindowsやmacOS，linux，FreeBSDなどのローカルまたは仮想ファイルシステムをサポートしている．

`-n`または`--dry-run`で転送されるファイルを確認できる．
```ps1
# クラウドストレージ全体をコピーする際は特にアスタリスク等は不要
$ rclone copy dropbox: ${path}
```

## Officeのインストール
基本的にOfficeはExcelとPowerPoint，Wordしか使わないのでそれ以外をインストールしないように設定する．
Micorsoft公式サイトの[リンク](https://docs.microsoft.com/ja-jp/deployoffice/overview-office-deployment-tool)からOffice Deployment Toolをダウンロードし，適当なディレクトリに解凍する．同じディレクトリ中に`${filename}.xml`を作成し，インストールしないソフトウェアを`<ExcludeApp ID="softwarename">`中に書き込む．
```xml
<Configuration>
  <Add OfficeClientEdition="32">
  	<Product ID="O365ProPlusRetail">
  		<Language ID="ja-jp"/>
  		<ExcludeApp ID="Access"/>
  		<ExcludeApp ID="Groove"/>    <!-- Onedrive for Business -->
  		<ExcludeApp ID="Lync"/>      <!-- Skype for Business -->
  		<ExcludeApp ID="OneDrive"/>
  		<ExcludeApp ID="OneNote"/>
  		<ExcludeApp ID="Outlook"/>
  		<ExcludeApp ID="Publisher"/>
  	</Product>
  </Add>
</Configuration>
```

以下のコマンドを実行すると管理者権限を求める画面が立ち上がり，あとは通常通りインストールが実行される．
```ps1
$ .\setup.exe /configure .\${filename}.xml
```

## sshログインできるようにしておく
クライアント側でキーペアを作成
```bash
$ mkdir .ssh
$ cd .ssh
$ ssh-keygen -t rsa
# このあと3回Enterを押す
```
ホスト側の準備
```bash
# OpenSSH-Serverのインストール
$ sudo apt install openssh-server
```
公開鍵を追加する
```bash
# パーミッションの変更
$ chmod 700 .ssh
$ cd ~/.ssh
$ chmod 600 id_rsa.pub
# 公開鍵の追加
# authorized_keysを新規に作成する場合
$ mv id_rsa.pub authorized_keys
# authorized_keysがすでに存在する場合，catコマンドで追記
$ cat id_rsa.pub >> authorized_keys
```
簡単にssh接続できるようにクライアントにエイリアスを追加
```bash
# ~/.ssh/config
Host hostname
    Hostname 'IP adress'
    User 'username'
    IdentityFile ~/.ssh/id_rsa

$ ssh hostname
```

## WSLのセットアップ
最も一般的にはパッケージ管理aptを利用する．
```bash
# パッケージ一覧の更新．こまめに行うようにする．特にリポジトリの追加・削除後には必ず行うこと
$ sudo apt update
# パッケージの更新
$ sudo apt upgrade
# インストール済みのパッケージを表示
$ apt list --installed
# パッケージのインストール
$ sudo apt install ${package}
# パッケージのアンインストール．完全に削除する場合は--purgeをつける
$ sudo apt remove ${package}
$ sudo apt remove ${package} --purge
# 不要になったファイルを削除．表示されたら実行する
$ sudo apt autoremove
```
debファイルからインストールする場合は以下の方法を使う．
```bash
$ sudo apt install ./${deb_filename}.deb
```
なお，`dpkg -i ${deb_filename}.deb`は古い方法であるため非推奨．

aptに代わる新しいパッケージ管理ツールとしてSnappyが推されている．
サンドボックス機能がありaptでインストールするよりセキュアらしい．
```bash
# パッケージを検索
$ snap find ${package}
# インストール済みのパッケージを表示
$ snap list --all
# パッケージをインストール．--classicが必要かどうかはfindで確認
$ sudo snap install ${package}
$ sudo snap install ${package} --classic
# アンインストール
$ sudo snap remove ${package}
# アップデート
$ sudo snap refresh
```
aptでインストールできる全てのパッケージをsnapでインストールできる訳ではないことに注意．
また，いくつかのアプリケーションはsnapでインストールすると正常に動かない．特に権限関係のエラーが多い印象．当面はaptでインストールするほうが無難．
- VScode: 日本語入力ができない
- rclone: "permission denied" と表示されクラウドストレージに接続できない（su権限ならできるかも？）
- tree: /mediaなどいくつかのディレクトリが開けない