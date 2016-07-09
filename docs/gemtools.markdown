---
title: GemTools on MagLev
layout: docs
gemtools_version: GemTools-1.0-beta.8.7-310x
---

# MagLev用Gemtools Smalltalk Browser

GemToolsはSmalltalkのソースコードをMagLevに構築するのに使われるSmalltalkの開発環境です。
同じ情報をTopaz(ビルトインコマンドラインデバッガー/インスペクター)によって得ることもできまが、GemToolsはイメージと対話式の良いGUIを提供しています。

GemToolsはPharo SmallltalkをLinuxとOSXで動かすのに必要な全てをダウンロードします。

## GemToolsのインストール

1. ダウンロード [{{page.gemtools_version}}](http://seaside.gemstone.com/squeak/{{page.gemtools_version}}.zip)
1. アーカイブを解凍
   `{{page.gemtools_version}}.app` このようなディレクトリ名にした方がいいです。

   OSXユーザーは `/Application` に移動したくなるでしょう。

## GemToolsの実行

1. MagLev stoneが実行されていることを保証する:

       $ cd $MAGLEV_HOME
       $ rake maglev:start

1. `netldi` プロセスが実行されていることを保証する:

       $ cd $MAGLEV_HOME
       $ rake netldi:start

1. GemToolsを起動

   OSXユーザーは `open {{page.gemtools_version}}.app` またはFinderから普通の
   OSXアプリとしてFinderから起動してください。

   Linuxユーザーは `{{page.gemtools_version}}.app/Pharo.sh` で起動してください。

1. MagLevに繋ぐ

   Pharo Smalltalkプロセスが立ち上がった後、MagLev stoneに繋げる必要があります。
   GemToolsランチャーウィンドウの"MagLev"を選んで"Login"をクリックしてください。

   始めてログインする時、氏名を尋ねられます。これは( `~/.gitconfig` にユーザー情報を追加するように)Monticelloソースコードシステムに誰が変更を加えるか伝えることができます。

## ソースコードを見る

イメージに入った後、MagLevのコードを見れます:

1. "Find..."ボタンをクリック
1. "Hierarchy browser"を選択
1. "Kernel"に入ってエンターキーを押すか"Ok"をクリック

ここからソースコードを探す事ができます。

## SmalltalkとMagLevの `netldi` を共有

もしGemStone/S Smalltalkユーザー、もしくはSmalltalkとMagLevを同じロックディレクトリで使いたいならなら `$MAGLEV_HOME/setupLocks.sh` を実行してください。
