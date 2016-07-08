---
layout: docs
title: Learn MagLev
---

# MagLevについて知る

## 基本的なMagLevオブジェクトの永続化

MagLevはRubyに透明、分散オブジェクトをもたらします。
試すには [MagLevを始めるには](/maglev.github.com/docs/get_started.html) を見てMagLevサーバーを起動してください。

まず、 `maglev-irb` (ここでは2つのirbセッションを使うので分かりやすいように"One"、"Two"とプロンプトを分けます)を起動します。

    $ maglev-irb
    One :001:0>

MagLevにはあらかじめ `Maglev::PERSISTENT_ROOT` という名前の永続化しているハッシュを持っています。
これは他のVMに共有したいオブジェクトを起き始めるのに便利です。
このハッシュにメッセージをおいて保存します:

    One :001:0> Maglev::PERSISTENT_ROOT[:demo] = "Message from maglev-irb #1"
    => "Message from maglev-irb #1"
    One :002:0> Maglev.commit_transaction
    => true

次に新しく `maglev-irb` セッションを起動してさっきのメッセージを見てみましょう:

    $ maglev-irb
    Two :001:0> Maglev::PERSISTENT_ROOT[:demo]
    => "Message from maglev-irb #1"

すごい!

MagLevの永続化モデルはpersistence by reachabilityです。
これはMagLevが `あるオブジェクト` を永続化(保存)したい時、 `あるオブジェクト` (下記の記事ではインスタん変数に保持されている)からアクセス可能な全てのオブジェクトも永続化されるということです。
`Maglev::PERSISTENT_ROOT` は単純に永続性のハッシュで、保存される時
(つまりキーと値)も保存されます(これらが参照するあらゆるオブジェクトも同様です)。

従って、"複雑な"オブジェクトを作成し、irbTwoプロセスからそれをirbOneプロセスに送り返すことができます。
まず、何かオブジェクトを入れた普通の配列を作ります:

    Two :002:0> my_array = Array.new
    => []
    Two :003:0> my_array << "Hello from #2"
    => ["Hello from #2"]
    Two :004:0> my_array << Time.now
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]

Then, we hook it into the persistent graph by putting the array into
`PERSISTENT_ROOT`, and committing the change:
それから、配列を `PERSISTENT_ROOT` に入れて、変化を委任することによってそれを持続的なグラフに接続します:

    Two :005:0> Maglev::PERSISTENT_ROOT[:demo] = my_array
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]
    Two :006:0> Maglev.commit_transaction
    => true

PERSISTENT_ROOTは永続性で、それは配列を参照し、そしてその配列はStringとTimeオブジェクトを持ち、他のVMでも使えるようになります。
最初の `maglev-irb` セッションに切り替えて確認します。

    One :003:0> Maglev::PERSISTENT_ROOT[:demo]
    => "Message from maglev-irb #1"

うーん、、、最初のirbセッションはまだ古い値を見ています。
共有されたオブジェクト空間に変更を調整するためMagLevはトランザクションを使います。
新しいデータを見るためにトランザクションをリフレッシュさせる必要があります:

    One :004:0> Maglev.abort_transaction
    => Maglev::System
    One :005:0> Maglev::PERSISTENT_ROOT[:demo]
    => ["Hello from #2", Mon Oct 17 13:45:52 -0700 2011]

これでirbTwoから新しいデータを見ることができます。標準的なRubyオブジェクトの多くはもう永続化することができます。(MutexやSocketのように幾つか他のVMで見えないものもあります)
`Proc` や `Thread` さえも永続化できます:

    One :006:0> Maglev::PERSISTENT_ROOT[:run_me] = Proc.new { puts "Hi from another VM" }
    => #<Proc>
    One :007:0> Maglev.commit_transaction
    => true

そして他のVMから実行できます:

    Two :007:0> Maglev.abort_transaction
    => Maglev::System
    Two :008:0> Maglev::PERSISTENT_ROOT[:run_me].call
    Hi from another VM

ProcをVMの間で渡すRubyのコード数行でシンプルな [worker queue](https://github.com/jc00ke/maglev-q) を作成できます。

Threadは(JITはメソッドをマシン語にコンパイルしていて、それはVMをまたぐことができないので)参照によって永続化されませんが、あるトリックを使えます。

    One :009:0> Thread.start { callcc {|cc| $cont = cc}; p "Run after One pretty callcc" }
    "Run after One pretty callcc"
    => #<GsProcess:0xdf69e01 false>
    One :010:0> Maglev::PERSISTENT_ROOT["cc"] = $cont
    => #<Continuation:0xecfc401 @_st_process=#<GsProcess:0xdf6a901 sleep>>
    One :011:0> Maglev.commit_transaction
    => true

何をしたのでしょうか？ええと、スレッドを作って永続化を始めましょう。
永続化は計算の状態を読み取り、永続化された時点でのスタックをコピーします。
(永続化されたオブジェクトが、作成されたThreadのコピーを参照することを
検査プログラムで確認することができます)
便利のため永続化されたオブジェクトをグローバル変数に代入し、Stoneレポジトリに
コミットしています。

2番目のVMで:

    Two :009:0> Maglev.abort_transaction
    => true
    Two :010:0> $cont = Maglev::PERSISTENT_ROOT["cc"]
    => #<Continuation:0xecfc401 @_st_process=#<GsProcess:0xdf6a901 sleep>>
    Two :011:0> Thread.start { $cont.call }
    "Run after One pretty callcc"
    => #<GsProcess:0xdd33b01 sleep>

スレッドを別のVMにコピーして再開できました。

より多くのコード例は [hat trick example](https://github.com/MagLev/maglev/tree/master/examples/persistence/hat_trick) にあります。
またこれらはMagLevの永続化の基本的なデモンストレーションでもあります。

