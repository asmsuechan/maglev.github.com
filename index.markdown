---
layout: default
title: MagLev
---
{% include fork_me.html %}

# MagLev

MagLevは高速で安定した 64bitの [オープンソースの](https://github.com/MagLev/maglev/blob/master/Licenses/README.txt) VMwareのGemStone/S 3.1 バーチャルマシンの上でビルドされたRuby言語とライブラリの処理系です。

MagLev VMは堅牢で耐久性のあるプログラミングプラットフォームを提供するためにフルに分散共有キャッシュや完全なACIDトランザクションやエンタープライズクラスのNoSQLデータ管理機能などのGemStone/S JITのネイティブコードの機能を利用します。
これはメモリに収まるより多くの、テラバイト級のデータやコードをそのまま管理することができます。
このデータにはオブジェクトやクラス、ブロック、スレッド、またストアされているか実行されているかなどの制限はありません。

MagLev 1.0.0 は2011年10月31日にリリースされました。

# MagLevを始める

もし分散されたRubyオブジェクトの永続化に興味があるなら[MagLevを始める](/maglev.github.com/docs/get_started.html) を見てください。

# Licensing & Pricing

MagLevは無料で [オープンソース](https://github.com/MagLev/maglev/blob/master/Licenses/README.txt) です。

MagLevを実行するにはGemStone/S サーバーが必要です。リファレンスがあります [Web Edition of GemStone/S](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) (a.k.a GLASS)。 あなたは自由に開発やMagLevアプリケーションの公開を [ライセンス](http://seaside.gemstone.com/docs/GLASS-License.pdf) とkeyfileにより課された [リソースの限界](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) に沿う限り自由に行っても良いです。
このフリーバージョンで小規模または中規模プロジェクトには十分です。
もし必要ならより良い性能の [Web Editions of GemStone/S](http://seaside.gemstone.com/docs/GLASS-Pricing-1201.htm) を購入できます。
