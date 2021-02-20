---
title: 計算機のセットアップ手順書
date: 2021-01-02
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

aptに代わる？新しいパッケージ管理ツールとしてSnappyが推されている．
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

- ufw: ファイアウォールの設定
- tmux: ターミナルマルチプレクサ
- ranger: ファイラ 
- tree: ディレクトリ構造の可視化
- NeoVim: エディタ
- VSCode: エディタ
- Mendeley: 文献管理
- Thunderbird: メーラー
- gimp: ビットマップイメージの加工
- inkscape: ベクタイメージの作成
- rclone: クラウドと同期（後述）
- Julia: 電卓
- ImageJ: 画像解析
- VLC: メディアファイル再生

## tmuxのメモ
`prefix`はデフォルトで`ctrl-b`に割り当てられている．
起動
```bash
$ tmux
```
セッションの確認
```bash
$ tmux ls
```
ペインの操作
| tmuxコマンド | 機能 |
| ---- | ---- |
| `prefix + "` | 水平分割 |
| `prefix + %` | 垂直分割 |
| `prefix + o` | ペイン間移動 |
| `prefix + x` | ペイン分割解除 |

コピーモード関係
| tmuxコマンド | 機能 |
| ---- | ---- |
| `prefix + [` | copyモード |
| `prefix + ]` or `q` | copyモード解除 |
| `Space` | copy開始位置指定 |
| `Enter` | copy終了位置指定 |
| `prefix + >` | クリップボードへ書き出し |

## Python環境の構築
pipとvenvをインストール．
- numpy: 多次元配列を扱える数値計算ツール
- scipy: 統計，科学計算
- pandas: データの整形
- matplotlib: データの可視化
- jupyterlab: アドホック分析ツール
- numba: 事前にコンパイルして計算を高速化

2020年1月時点で最新版のPython3.9系はnumbaに対応していないのでバージョンを指定してインストールする．
```
$ scoop install python@3.8.5
```
必要なライブラリをインストール．
```ps1
$ python3 -m venv ${env_name}
$ source ${env_name}/Scripts/Activate.ps1
(${env_name})$ pip3 install numpy scipy pandas matplotlib jupyterlab numba
```

## rclone
Ubuntuはクラウドストレージの公式クライアントが提供されていない場合が多々あることが問題だが，その代替となるソフトウェア．
言うなればクラウドストレージ版rsync．
```ps1
# クラウドストレージ全体をコピーする際は特にアスタリスク等は不要
$ rclone copy dropbox: ${path}
```

## エディタ（NeoVim）の設定
init.vimの設定
```vim
set number "行番号を表示
set tabstop=4 "タブでスペース4つ
set shiftwidth=4 "自動インデント時にスペース4つ挿入
set clipboard=unnamed "yankした文字列をクリップボードにコピー
set hls "検索した文字をハイライト
set mouse=a "マウス操作に対応
set list "空白文字を可視化
```

## メーラ (Thunderbird) の設定
GmailのSMTPサーバの設定は以下の通り．なお，大学のアドレス（@st.yamagata-u.ac.jp）もGmail扱いになったので同様に設定できる．
- Server Name: smtp.gmail.com
- Port: 465
- Connection security: SSL/TLS
- Authentication method: OAuth2
- User Name: 使用するメールアドレス

OKを押しても特にパスワードを求められないが，最初の送信時に認証画面が表示されるのでそこにパスワードを入力する．

複数のアドレスを一つのGmailアカウントに転送している．
受信したメールはGmailのフィルタによって自動的にアカウント別に振り分けることができるのだが，Thunderbird側から送信するメールはGmail側でフィルタを設定しても適用されない．
そのためThunderbird側でフィルタを設定する必要がある．

テキスト形式でやりとりしたいので，"Compose messages in HTML format" のチェックを外す．

使い勝手を良くするため，以下のアドオンをインストールする．主にGmailとの連携に関するもの．
- CardBook: Gmailのアドレス帳と同期する．以前はgContactSyncというエクステンションがあったが現在は使用不能？
- Provider for Google Calendar: GoogleカレンダーのiCalリンクを貼り付けても読めるがThunderbird側からの書き込みができないのでこれを使う．
- Send Later: 時間指定送信機能を追加．

Firefoxと異なりThunderbirdは設定を同期することができない．そこでメールや設定データが格納されているプロファイルフォルダをクラウドで同期することを考える．
- `%AppData%Roaming\Thunderbird\Profiles`に`default-release`というフォルダがあるのでこれを移動する．
- `%AppData%Roaming\Thunderbird`にある`profiles.ini`の`Path=`以降を移動先に書き換える．


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
```
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

## クライアント側の準備
- Windows側では6000番ポートを開放
- 以下のurlから[VcXsrv](https://sourceforge.net/projects/vcxsrv/)をインストール

## ホスト側の準備
- X転送ができるようにIPアドレスを追加
```bash
# IPアドレスを追加しておく
$ export DISPLAY='クライアントのIPアドレス':0.0
$ echo $DISPLAY
'クライアントのIPアドレス':0.0
```