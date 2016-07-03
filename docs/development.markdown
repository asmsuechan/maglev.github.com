---
title: MagLev Development
layout: docs
---
# MagLevを開発する

## ステップ1: GitHubからMagLevをインストール

初めにMagLevを[ここ](/maglev.github.com/docs/build.html)で説明したようにソースコードからビルドします。また、テストが通ることを確認してください。

## ステップ2: 環境構築

ソースコードからMagLevをインストールした場合、<tt>bin</tt>フォルダからコマンドを実行しなければなりません。
もしあなたがrbenvを使っているのなら<tt>rbenv/.versions/maglev-head</tt>にチェックアウトしてリンクするか <tt>PATH</tt> 環境変数などを設定できます。
これは、幾つかのコマンドは他のRubyの <tt>rake</tt>やMagLevから実行しなければならないので非常に重要なことです。
It's important you do
manage it somehow, because some commands have to be run with another
Ruby's <tt>rake</tt>, and some can be run from MagLev.

開発者のために書かれたスクリプトやrakeタスクのいくつかは環境変数のセットを必要とします。
ですので、MagLevをcloneしたフォルダのトップで以下を実行してください:

    $ export MAGLEV_HOME="$(pwd)"
    $ export GEMSTONE_GLOBAL_DIR=$MAGLEV_HOME
    $ export GEMSTONE="$MAGLEV_HOME/gemstone"

## ステップ3: 第一原理よりstoneをビルド

もしこの[MagLevをビルドする](/maglev.github.com/docs/build.html)を終えているなら、既に動いているイメージがあるはずです。
ですが、いくつかの方法でstoneプロセスを壊したり作ったりする場面に遭遇することがあるでしょう。私たちは自動でプロセスを渡りGemstone/Sで提供されるSmalltalkイメージから新しいイメージ作ります。

説明したそれぞれのステップにおいて何が起きているかや特定の領域でどうやって開発したいかを説明します。
At each step I'll explain what's going on and how you might want to
develop in that particular area.

<div style="padding: 2em;">
<em>Note:</em>
これらのコマンドの多くは他のRubyで実行されるべきです。
MagLevには標準で <tt>ruby</tt> という名前のスクリプトを含んでおらず、代わりに <tt>maglev-ruby</tt> コマンドのみが存在します。
混乱を避けるため、 <tt>$MAGLEV_HOME/bin</tt> が <tt>$PATH</tt> 環境変数の最後にあることを確認してください。
私たちがMagLevで何かを動かす時はいつも <tt>maglev-ruby -S</tt> を使っています。
</div>

### 全てを消す

イメージが壊れたと思ったりいくつかのコードが永続化されて消す方法が分からない時などは以下のclobberタスクを実行するだけでRubyイメージが削除されます。

    $ rake build:clobber

### Smalltalkイメージの準備

RubyとSmalltalkは同じVMとイメージで動きますが、SmalltalkイメージがRubyコードを実行するためにいくつかの違いがあります。
そのコードは <tt>src/smalltalk/ruby</tt> にあります。
これらはSmalltalkのコアクラスの拡張となっており、普通は気にしなくてもいいです。
もしあなたがこれらのどれかを変更する必要があるなら、イメージを一度clobberで削除して新たに起動するべきです。
そしてRubyイメージを以下のコマンドで作成することができます。

    $ rake build:filein

このコマンドはRubyコードを組み込みます (Smalltakerはコードをロードしてそれをイメージに永続化するためのコードを書きます) また、Monticello(SmalltalkのVCS)も同様にSmalltalkイメージからStoneに接続するために [GemTools](/maglev.github.com/docs/gemtools.html)を使います。

もしこのタスクを2回走らせたら2回目は失敗してイメージを使用不可状態にします。
If you run this task twice in succession, the second run will fail and
possibly leave your image in an unusable state.

### Rubyコアのロード

You know how, on MRI, a lot of core functionality is implemented in C?
MagLevを使っている時、もしあなたがCが好きでないなら少しいい状況かもしれません。
If you don't like C, then on MagLev, the situation is a little nicer
基本構造やC/C++で実装されたパーサ
Rubyコアライブラリの多くは実はSmalltalkで書かれている。
for you. While the VM, the primitives and the parser are implemented
in C/C++, lots of what makes up the Ruby core library is actually
written in Smalltalk. That code used to be managed in Monticello, and
そのコードはMonticelloで管理されたものであり、今 <tt>src/packages</tt> に含まれています。
これは実は[FileTree](https://github.com/dalehenrich/FileTree)であり、まだ[GemTools](/maglev.github.com/docs/gemtools.html)のMonticelloからそれらのパッケージを管理できます。

    $ rake build:packages

このコマンドは(Smalltalkで書かれている限り)Rubyコアコードをstoneで起動します。
もしMagLevで開発するなら、2つの方法があります:
That invocation will load the Ruby core code (as far as it is written
in Smalltalk) into the stone. If you want to develop on MagLev, you
might work a lot with this code, and there's two ways to do that:

##### Smalltalker用

Developers who know either Gemstone/S or Squeak/Pharo and are
comfortable working with Monticello and the image based tools should
get the [GemTools for MagLev](/docs/gemtools.html).

Monticello uses FileTree to update the files whenever a new version of
the Monticello package for MagLev is saved using the UI, so you don't
need to worry about keeping the code in sync. If you develop from the
GemTools browser, you won't have to repeat the <tt>build:packages</tt>
command either, because all your changes are immediately persisted in
your image and applied to your running code.

##### For friends of the text editor

You can just edit files in the <tt>src/packages</tt> directory. If you
add a class, check how other classes work (separate folder for the
class, json file with the definition and method names, folders for
instance methods and class methods). If you need to extend a core
Smalltalk class, take a look at the folders ending in
<tt>.extension</tt>.

### Create a stone

These last two steps have left us with an image called
<tt>extent0.ruby.dbf</tt>. It's in the <tt>fileintmp</tt>
directory. Now, to create a stone, MagLev expects the image file to be
in <tt>bin/</tt>, but we'll want to be able to just run the
<tt>build:packages</tt> task again to update Smalltalk code in our
stones. So let's link the image file instead:

    $ ln -s $MAGLEV_HOME/fileintmp/extent0.ruby.dbf bin/extent0.ruby.dbf

Then create a stone

    $ rake stone:create[maglev]

This will copy the image file to
<tt>data/maglev/extent/extent0.ruby.dbf</tt> and that's what we'll be
running off when running Ruby code. So we symlink that, too:

    $ rm data/maglev/extent/extent0.ruby.dbf
    $ ln -s $MAGLEV_HOME/fileintmp/extent0.ruby.dbf data/maglev/extent/extent0.ruby.dbf

### Load the Ruby core

Now, finally, we can actually run some Ruby code. Nothing too serious, yet,
but enough to load the other half of Ruby core written in Ruby. Much
of that code was actually shared with the [Rubinius](http://rubini.us)
project at some point in the past.

    $ rake maglev:start
    $ rake maglev:reload_prims

This starts the stone process and loads the code below
<tt>src/kernel</tt>. That means all the Ruby code in there will be
parsed, compiled and committed to the image, so subsequent VMs don't
have to reload all of the Ruby core when they start up. Whenever you
change any code in there, you'll have to run the
<tt>maglev:reload_prims</tt> task again.

If everything went as it should, you can now use MagLev to run useful
Ruby code. Like this:

    $ maglev-ruby -e 'p MAGLEV_VERSION'
    "1.0.0"

## ステップ4: 大きな喜び

あなたは基本的な開発環境を作りました。あなたは機能開発や[バグ修正](https://github.com/MagLev/maglev/issues)ができるようになりました。
もしどこに問題があるか分からない時は、どこに何があるか知るためにコードを眺めましょう。

## 進んだトピック

#### Topazでコードを見る

もし <tt>src/kernel</tt> にRubyコードを見たら、<strong><tt>原始的</tt></strong> なクラスレベルの呼び出しを見つけることができます。
If you look through the Ruby code in <tt>src/kernel</tt>, you will
find class-level calls to <strong><tt>primitive</tt></strong>. These
これらはSmalltalkのメソッドをRubyのメソッドにバインドします。
これらのSmalltalkのメソッドは <tt>src/smalltalk</tt> や <tt>stc/packages</tt> ディレクトリでよく見ますが、これらはイメージにしかすぎません。
bind Ruby method names to Smalltalk methods. These Smalltalk methods
are often found under either the <tt>src/smalltalk</tt> or
<tt>src/packages</tt> directories, but they may also be just in the
image. To look at what's currently available in the image, you can use
イメージ中で何が現在使用可能か見るためにGemtools(上述)か、GemStone/Sにある <strong>topaz</strong> を使います。
Gemtools (see above), or <strong>topaz</strong>, which ships with
GemStone/S.

環境変数がセットされていて、maglevのホームディレクトリにいる場合、以下を実行してください:

    $ bin/maglev startnetldi
    $ gemstone/bin/topaz
      *snip*
      topaz> set user DataCurator password swordfish gemstone maglev
      topaz> login
      *snip*
      successful login
      topaz 1>

これは(デフォルトユーザーの)DataCuratorとしてstone "maglev"にログを取りました。

    topaz 1> look Module >> rubyName
    
    category: '*maglev-runtime'
    method: Module
    rubyName
      ^ name
    
    %
    topaz 1>

You've just looked at the method rubyName in the class module, as it
is currently loaded in the stone (useful for comparing what's in your
files and what's in the stone).

    topaz 1> set class Object
    topaz 1> rubylook meth inspect
    method: Object
      inspect#0*&
        <bridge method, objId 188217345>
    method: Object
      def inspect
        # sender of _inspect must be in env 1
        self.__inspect
      end
    # method starts at line 324 of /home/tim/Devel/maglev/src/kernel/bootstrap/Object.rb
    topaz 1>

And here you looked at the Ruby implementation of
Object#inspect. Notice how the stone sees a method
<tt>inspect#0*&amp;</tt> - this encodes the information how many
arguments a method takes.

    topaz 1> rubylook meth class
    method: Object
      class#0*&
        <bridge method, objId 188189697>
    method: Object
    class
    "return first non-virtual non-singleton class at or above receiver's class.
    
     The old method  Object>>_class is not implemented in extent0.ruby.dbf
    "
    <primitive: 730>
    ^ self _primitiveFailed: #class
    topaz 1>

This is how a primitive looks in the stone. You have a bridge method
like for a pure Ruby method, but as implementation, the stone has the
Smalltalk method class (which is VM primitive 730).

Topaz has many more features, you can run and edit code. Type
<tt>help</tt> and/or refer to the
[GemStone/S Programming Guide](http://community.gemstone.com/download/attachments/6816350/GS64-ProgGuide-3.0.pdf)
to learn more. This will also be useful when
[debugging](/docs/issue_tracking.html).

