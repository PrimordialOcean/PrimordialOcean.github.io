# ImageMagick覚書
## ImageMagickとは？
ImageMagickはコマンドライン上で利用できるビットマップイメージの作成，編集，変換などを行うツール．
PNG, JPEG, GIF, TIFF, Postscript, PDF, SVGなどの200以上のファイル形式に対応している．
マルチスレッド処理に対応しているためGB単位の巨大なファイルでも高速に処理できることが特徴．
公式サイトは [https://imagemagick.org](https://imagemagick.org)．

## インストール
インストールは特にこだわりがなければaptでインストールできる．
```
$ sudo apt install imagemagick
```

## 使い方
主に以下の用途に利用している．
- 大量のビットマップイメージのファイル形式を一括で変換する．
- スキャンした連番の画像ファイル（JPEG等）から1つのPDFファイルを作成．

例えば，"hoge.jpg" というファイルを "hoge.png" に変換する場合は以下のコマンドを実行する．
```
magick hoge.jpg hoge.png
```
複数ファイルのファイル形式を変換する際，シェルスクリプトで再帰的に処理する方法が紹介されることがあるが，`mogrify`コマンドを用いれば簡単に可能．
```
$ mogrify -format "変換後の拡張子" *."変換前の拡張子"
```

## メモ
- convertとmagickどっち？：ImageMagick 6まではconvert，ImageMagick 7以降はmagickコマンドを使う．
Windowsには「FATボリュームをNTFSに変換するコマンド」として`C:\Windows\System32\convert.exe`が存在している．
Windowsのconvertとと混同した場合，最悪Windowsシステム自体を破壊しかねないため，ImageMagickのconvertコマンドがWindowsのconvert.exeより先に見つかるようにPATHの先頭に追加しておく．
うっかりミスを防ぐため，ImageMagick 7以降を使用するか，WSL上にImageMagickをインストールして利用することを推奨．ただし，2020年12月現在Ubuntu 20.04LTSにaptでインストールされるImageMagickのバージョンは6であることに注意．

- PDFやPS形式に変換しようとすると "not authorized" エラーが表示される：ImageMagickのセキュリティーポリシーの変更のため制限されている．
`/etc/ImageMagick-6/policy.xml`をsu権限でvim等のテキストエディタ開き，以下の変更を加える．
```
-<policy domain="coder" rights="none" pattern="PDF" />
+<policy domain="coder" rights="read|write" pattern="PDF" />
```
- convertコマンドを実行した際に "cache resources exhausted" エラーが表示される：メモリキャッシュの枯渇が原因．`policy.xml`のメモリの値を256MiBから適当な値に変更する．
```
-<policy domain="resource" name="memory" value="256MiB"/>
+<policy domain="resource" name="memory" value="4096MiB"/>
```
[Return to the home page](../index.md)
