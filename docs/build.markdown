---
title: MagLev Build
layout: docs
---
# ソースからMaglevをビルドする

## ステップ1: Maglevのソースコードをインストール
MagLevのソースなどを保存するための新しいディレクトリを(NFSではなく)ローカルに作成します。MAGLEV_HOMEにパスを通してください:

    $ git clone git://github.com/MagLev/maglev.git
    $ export MAGLEV_HOME=$PWD/maglev

## ステップ2: VMをインストール

    $ cd $MAGLEV_HOME
    $ ./install.sh

特定のVMバージョンを指定したいのならこちらの詳細 <https://github.com/maglev/maglev> をご覧ください。

## ステップ3: MagLevイメージをビルドして起動

以下のコマンドはベースSmalltalkイメージをGemStone/Sに変換します。
(<tt>$MAGLEV_HOME/gemstone/bin/extent0.dbf</tt>) をRubyイメージにし、ここに保存します <tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>:

    $ rake build:maglev
    $ rake maglev:start

これでMagLevをRuby上で実行できるようになります:

    $ maglev-ruby -e 'puts "Hello from #{RUBY_ENGINE}"'
    Hello from maglev

テストを実行します:

    $ rake spec:ci

# 全てを組み合わせるには

MagLevは、最適なパフォーマンスのためC++で書かれたGemStone/Sバーチャルマシンの最上位レイヤーに位置します。
MagLevをビルドするために私たちはMagLev用に拡張された基本的なGemStone/Sシステムを起動します。
一度MagLev VMが必要なプログラムと共にロードされると他の実装のようなRubyコードが実行できるようになります。
MagLevには複数のVMの共有メモリを介するトランザクションの相互作用にある透明オブジェクトの永続性と共に統合された分散オブジェクトキャッシュを使うという違いがあります。
大量のデータを処理するために、MagLevシステムには別のVMで動くマルチスレッドガーベジコレクションがあります。
多言語の性質のため、MagLevはRuby、Smalltalk、C++(C++へのFFIを通じて、Rubyの拡張はC++で直接書かれています)のコードを利用します。

上に書いたビルド手順はRubyの基本的なクラスとオブジェクトを標準的なGemStone/Sシステムのトップに追加します。そしてこれらをベースMagLevイメージとして永続化します。 (<tt>$MAGLEV_HOME/bin/extent0.ruby.dbf</tt>)

Rubyオブジェクトをそのイメージで読み込むスクリプトはrakefile(<tt>rakelib/build.rake</tt>)とtopazスクリプトの組み合わせです。
TopazはGemStone VMで動くC++のプログラムで、VMのコマンドラインインターフェースを提供します。以下も参考にしてください。
[Topaz Programming Guide](http://community.gemstone.com/download/attachments/6816350/GS64-Topaz-3.0.pdf?version=1).
