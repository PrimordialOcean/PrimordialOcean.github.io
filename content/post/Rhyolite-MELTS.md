---
title: Rhyolite-MELTSについて
date: 2020-04-16
categories:
  - "Articles"
tags:
  - "Earth Science"
  - "MELTS"
  - "PC"
---
Rhyolite-MELTSの使い方についてメモ．
<!--more-->

## Rhyolite-MELTSとは
Rhyolite-MELTSとは相平衡計算ソフトMELTS (Ghiorso and Sack, 1995) の改良版である．
従来のMELTSとの主な変更点は以下の2つである．
- 石英とカリ長石端成分のエンタルピーを変更したことにより含水珪長質メルトに適用が可能になった．
- 数値計算のアルゴリズムが変更され，計算の安定性が向上した．

Rhyolite-MELTSの適用範囲はMELTSと同様で，500-2000℃, 0.1-2000 MPaの系に適用可能である．より高温・高圧の条件に適用可能なxMELTSやmdMELTSも開発されている．
注意点としてRhyolite-MELTSはソリダスないしニアソリダスでの計算に適していない．また，角閃石や黒雲母が晶出する系にも向かないとされている．

使用されている相平衡実験のデータは[MELTS公式サイト](http://melts.ofm-research.org/database.html)にまとめられている．また使用した結果を公表する際は，以下の論文を引用することが推奨されている．
- Gualda G.A.R., Ghiorso M.S., Lemons R.V., Carley T.L. (2012) Rhyolite-MELTS: A modified calibration of MELTS optimized for silica-rich, fluid-bearing magmatic systems. Journal of Petrology, 53, 875-890.
- Ghiorso M.S., Gualda, G.A.R., (2015) An H2O-CO2 mixed fluid saturation model compatible with rhyolite-MELTS. Contributions to Mineralogy and Petrology 2015, in press

## Rhyolite-MELTS：Ubuntu 20.04 LTSでの環境構築
以下の環境で動作確認を行った．
- マシン:Lenovo ThinkPad x1 Carbon 2018年モデル（Intel Core i7-8550U @ 1.80GHz, RAM 16 GB）
- ホストOS：Windows 10 Home (ver. 2004)
- 仮想マシン：VMware 16
- 仮想OS：Ubuntu 20.04 LTS

以下の作業はすべて仮想マシン上のUbuntu 20.04 LTS上で行っている．

（日本語Remix版のみ必要）ディレクトリ名を日本語->英語にする．
```
$ LANG=C xdg-user-dirs-gtk-update
```
ダイアログが表示されたら "Update names" をクリックする．
ディレクトリ内にファイルが入っていると日本語名のものが残ってしまうので注意．

[Rhyolite-MELTSのWebサイト](http://melts.ofm-research.org/unix.html)から "64-bit Ubuntu 10.04 rhyolite-MELTS/pMELTS" をクリックしてダウンロードする．
実際には "rhyolite-MELTS-Ubuntu-14.04-64bit.zip" がダウンロードされる．適当なディレクトリに保存して展開する．
```
$ cd "zipファイルをダウンロードしたディレクトリ"
$ unzip "zipファイル名"
```

`Melts-rhyolite-public`を実行しようとすると，次のようなエラーが発生することがある．
```
$ ./Melts-rhyolite-public
./Melts-rhyolite-public: error while loading shared libraries: libpng12.so.0: cannot open shared object file: No such file or directory
```
これはRhyolite-MELTSが動作するのに必要なライブラリの一つである`libpng12-0`がUbuntu 16.04以降のバージョンにはデフォルトで入っていないためである．
libpng-12のリポジトリを追加してインストールする．
```
$ sudo add-apt-repository ppa:linuxuprising/libpng12
$ sudo apt update
$ sudo apt install libpng12-0
```
## 使用方法
（最初の起動時のみ）`Melts-rhyolite-public`に実行権限を付加する．
```
$ chmod 755 Melts-rhyolite-public
```
`./Melts-rhyolite-public`を実行すると初めにバージョンの選択画面が現れる．

- ver. 1.0：MELTSモデルにおける石英とサニディンの自由エネルギーを補正したもの．流体相にはH2Oのみを想定している．
- ver. 1.1：揮発性成分の扱いがCO2も加味したGhiorso and Gualda (2015) のものに変更されている．石英が飽和している場合にのみ使用すべきと強調されている．
- ver. 1.2：ver 1.0の揮発性成分の扱いをアップデートしたもの．

[MELTSの公式ページ](http://melts.ofm-research.org/MELTS-decision-tree.html)にどのバージョンを使うべきかが示されている．それによれば，揮発性成分が含まれない場合にはver. 1.0.x.を，含まれる場合のうち石英に飽和している場合にはver. 1.1.x.を，そうでない場合には最新版のver. 1.2.x.を用いるべきと書かれている．
ver. 1.2.0を選択する場合の画面を例として示す．
```
$ ./Melts-rhyolite-public
---> Default calculation mode is rhyolite-MELTS (v. 1.0.2).  Change this? (y or n): y
---> Set calculation mode to rhyolite-MELTS (public release v 1.1.0)? (y or n): n
---> Set calculation mode to rhyolite-MELTS (public release v 1.2.0)? (y or n): y
```
### 簡単な使い方
1. 画面左側の"Bulk System"の空欄に組成を入力する．このときいくつかの微量元素（Cr2O3, MnO NiO, CoO）や揮発性成分（CO2, SO3, Cl2O-1, F2O-1）については入力しなくても計算には問題ない．
1. それ以外の元素については空欄の場合計算が破綻する可能性があるため，念のため小さい値 (e.g. 0.001) を入力しておく．
1. "Intensive Variables"から，"T, P"をクリックして温度・圧力を，"fO2 Content"から酸素バッファーを入力する．
1. "Commands"->"Execute/Halt"をクリックして計算を開始．終了するとMelts-rhyolite-publicと同じフォルダに計算結果が保存されている．

### 計算結果の見方
計算結果として"{mineral}.tbl"と"melts.out"ファイルが生成される．tblファイルは拡張子をcsvに書き換えておくと取り扱いが楽．

| Index | T (C) | P (kbars) | log(10) f O2 | mass (gm) | rho (gm/cc) | wt% SiO2 | ... | G (kJ) | H (kJ) | S (J/K) | V (cc) | Cp (J/K) |
| ---   | ---   | ---       | ---          | ---       | ---         | ---      | --- | ---    | ---    | ---     | ---    | ---      |
| インデックス |温度 | 圧力 | 酸素分圧 | 質量 [g] | 密度 [g/cc] | 元素組成 [wt%] | ... | 自由エネルギー | エンタルピー | エントロピー | 体積 | 比熱 |

## 計算における注意点
[JAMSTEC上木賢太さんのページ](https://sites.google.com/site/kentaueki/melts#004)に以下の指摘がなされている．筆者も同様の事象が発生することを確認済み．
- 水に飽和している条件で"Find liquidus"を行うとリキダスが決まらずに計算が暴走する．これを回避する場合は"Find liquidus"を使わず，計算開始温度を確実にリキダスより高い温度 (1400℃とか) に決め打ちしていきなり"Execute/Halt"を行う．
 
## 参考文献
- MELTS公式：[http://melts.ofm-research.org/](http://melts.ofm-research.org/)
- Topic: Updated: enabling the WSL and installing the Rhyolite-MELTS GUI (new for 2018)  (Read 3075 times)：[https://magmasource.caltech.edu/forum/index.php?topic=841.0](https://magmasource.caltech.edu/forum/index.php?topic=841.0)
- JAMSTEC上木賢太さんの解説：[https://sites.google.com/site/kentaueki/melts#004](https://sites.google.com/site/kentaueki/melts#004)
- Trouble installing libpng12 on 19.04: [https://askubuntu.com/questions/1137814/trouble-installing-libpng12-on-19-04](https://askubuntu.com/questions/1137814/trouble-installing-libpng12-on-19-04)