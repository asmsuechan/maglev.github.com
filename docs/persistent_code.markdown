---
layout: docs
title: Persistent Code
---

## コードの永続化

MagLevはRubyコードもRubyオブジェクトと同様に永続化します。一度コードを
インストールして全てのVMで共有します。MagLevは永続化と永続化しないコードを
持たせます。デフォルトでは、MagLevがrubyファイルを読んだ時、そのコードは
一時的にロードされます。これは通常のRubyのそれぞれのVMがコードをロードし、VMが存在する時そのコードが消えるのと同じようなものです。

## <code>Maglev.persistent do ... end</code>

この例を実行するためにhat_trickディレクトリに移動してください:

    $ cd $MAGLEV_HOME/examples/persistence/hat_trick

`rabbit.rb` には1つのメソッドがシンプルなクラスに定義されています:


{% highlight ruby %}
# このクラスはアスキーアートのうさぎを定義します
class Rabbit
  def inspect
    "\n () ()\n( '.' )\n(\")_(\")\n"
  end
end
{% endhighlight %}

このファイルを実行しても何も起こりません。VMが起動して、このコードを読み、 `Rabbit` クラスを定義し、次にexitし、全てを破壊します。
`Rabbit` には新しいVMからアクセスできません:

    $ maglev-ruby rabbit.rb 
    $ maglev-ruby -e 'puts Rabbit.new.inspect'
    ERROR 2730 , uninitialized constant Rabbit (NameError)

`maglev-ruby` は `Maglev.persistent` ブロックのコンテキストで実行する `-Mcommit` オプションを提供しています。ですので以下を実行できます:

    $ maglev-ruby -Mcommit rabbit.rb 
    $ maglev-ruby -e 'puts Rabbit.new.inspect'

     () ()
    ( '.' )
    (")_(")

`maglev-ruby -Mcommit rabbit.rb` は以下と等しいです:

{% highlight ruby %}
Maglev.persistent do
  load 'rabbit.rb'
end
Maglev.commit_transaction
{% endhighlight %}

モンキーパッチのクラスも永続化できます。 `breed.rb`という新ファイルを作成します:

{% highlight ruby %}
Maglev.persistent do
  # Monkey patch the Rabbit class
  class Rabbit
    def self.breed
      5.times.map { Rabbit.new }
    end
  end
end
Maglev.commit_transaction
{% endhighlight %}

このコードには `永続化` ブロックがあるので以下を実行するだけでいいです:

    $ maglev-ruby breed.rb

新しいVMをスタートして、すぐに新しいメソッドにアクセスしましょう:

    $ maglev-irb
    irb(main):001:0> Rabbit.breed
    => [
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
     () ()
    ( '.' )
    (")_(")
    ,
    ]
    irb(main):002:0>

# 詳細

詳細は [永続化ドキュメント](https://github.com/MagLev/maglev/blob/master/docs/persistence-api.rdoc) をご覧ください。

