---
title: R言語メモ
date: 2021-02-22
categories:
  - "Articles"
tags:
  - "R"
---

R（GNU R）はフリーの統計解析およびグラフィックスのための言語であり，モデリング，検定，時系列解析などの様々な統計用ツールを利用できる．公式サイトは[https://cran.r-project.org/](https://cran.r-project.org/)．R言語の環境構築，基礎的なコマンド，研究で有用なパッケージの使い方についてまとめる．

<!--more-->

## 環境構築
- OS: Windows 10 Home (20H2)
- CPU: AMD Ryzen 5 3600

Windows用パッケージマネージャであるScoopを使用してインストールを行う．
Scoop自体のインストールについては[作者のGitHub](https://github.com/lukesampson/scoop)を参照．
```ps1
$ scoop install r rtools
```
rtoolsはパッケージをビルドするためのツールである．CRANからパッケージをインストールするときに，ソースコードからビルドする必要がある場合がある．

Rのインタラクティブシェルを実行するには`rterm`を，スクリプトを実行するときには`rscript`を実行する．インタラクティブシェルから抜けるには`q()`を実行する．
```ps1
$ rterm
> q()
Save workspace image? [y/n/c]: y
```

パッケージをインストールするにはインタラクティブシェル上で`install.packages()`を実行する．初回だけCRANのミラーサイトを選択する画面が表示される．特にこだわりが無ければJapanを選ぶ．ダウンロードされたパッケージは`C:\Users\$Username\AppData\Local\Temp\RtmpU5g0Rt\downloaded_packages`に格納される．
```ps1
> install.packages("ggplot2")
```
有用そうなパッケージを以下にまとめる．
- `ggplot2`：データの可視化用ライブラリ．Pythonの`matplotlib`に近い．
- `ggpubr`：ggplot2よりもデフォルトで論文向きの図を作ることができる．
- `openxlsx`：Excelファイルの読み書き．
- `remotes`：GitHub上のパッケージを利用できる．

## 使い方
### パッケージのインポート
`library(packagename)` で導入できる．

### 関数定義
関数名は英語動詞で開始し，lowerCamelCase（小文字で書き始め，単語の先頭が大文字）にする．
```r
# 関数定義
getExcelData <- function(filename) {
    data <- read.xlsx(filename)
    return(data)
}
df <- getExcelData("compdata.xlsx")
```

### 図の出力

## 参考になるサイト
- [R公式](https://cran.r-project.org/)
- [RjpWiki](http://www.okadajp.org/RWiki/)
- [R基本統計関数マニュアル](https://cran.ism.ac.jp/doc/contrib/manuals-jp/Mase-Rstatman.pdf)
- [統計・データ解析（三重大学名誉教授奥村晴彦先生のHP）](https://oku.edu.mie-u.ac.jp/~okumura/stat/)
- [私達のR：ベストプラクティスの探求](https://www.jaysong.net/RBook/)