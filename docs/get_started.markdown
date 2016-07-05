---
title: Get Started with MagLev
layout: docs
---

# MagLevを始める

1. [RVM](http://rvm.beginrescueend.com)でMagLevをインストールする:

       rvm install maglev
       rvm use maglev

   もしくはこちらの [詳細](/maglev.github.com/docs/build.html) から
   ソースコードからビルドしてください。

2. MagLevサーバーの状態を確認

       $ maglev status
       MAGLEV_HOME = /users/pmclain/.rvm/rubies/maglev-1.0.0
       Status   Version    Owner    Pid   Port   Started     Type  Name
       ------- --------- --------- ----- ----- ------------ ------ ----
         OK    3.1.0     pmclain   48498 55390 Oct 31 10:06 Stone  maglev
         OK    3.1.0     pmclain   48499 55382 Oct 31 10:06 cache  maglev~3330cca3ca1d1f74

   もしサーバーが起動していなかったら以下のように出力されます:

       gslist[Info]: No GemStone servers.

3. Rubyを実行しましょう!

       $ maglev-ruby $MAGLEV_HOME/examples/hello_maglev.rb
       Hello from:
         RUBY_VERSION: 1.8.7
         RUBY_ENGINE: maglev

# 次は

* [MagLevについて知る](/maglev.github.com/docs/learn.html)
* MagLevを使ってみる [MagLev examples](https://github.com/MagLev/maglev/tree/master/examples)

