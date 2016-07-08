---
layout: default
title: Install MagLev
---
# MagLevのインストール

MagLevをインストールするには3つの方法があります:

* [Ruby Version Manager(RVM)でインストール](#install_with_ruby_version_manager)
* [GitHubからインストール](#install_from_github)
* [特定のMagLevリリースをインストール](#install_a_specific_maglev_release)

## 必要環境

* 64-bit hardware. There are no plans for a 32-bit version.
* 64ビットハードウェア。32ビット対応の予定はありません。
* 64ビットLinux、Mac OS XまたはSolaris X86。
* Rubyとrakeをインストールに使用します。
* 共有メモリの設定にroot権限を使用します(インストールの初期化のみ)

# RVMをインストール

[RVM](http://rvm.beginrescueend.com) は様々なバージョンのRubyやMagLevの実装を比較したい時に便利です。
以下のようにしてインストールします:

    rvm install maglev

RVMの詳しい使い方は[RVMのホームページ](http://rvm.beginrescueend.com) を見てください。

RVMは `rvm use maglev` を打った時自動でMagLevサーバーを起動します。
このサーバーは `rvm use any_other_Ruby` を打つと実行されたままになります。

MagLevサーバーを停止させる:

    rvm use maglev
    maglev stop

Note: `maglev stop` はただMagLevの現在使ってるバージョンを止めるだけです。
もしもっと使いたいなら、 `maglev stop` を以下のもののそれぞれで実行してください。

    rvm use maglev-1.0.0
    maglev stop
    rvm use maglev-head
    maglev stop

ProTip: 全てのMagLevサーバーをkillするには、 `ps -ef | grep /sys/stoned` して `stoned` プロセスをkillしてください。
これは最初に作業をするので停止する前に少し時間がかかります。

Note: MagLevからrakeを動かした時、 `rake maglev:stop` のようなmaglevタスクを実行することはできません。
`bin/maglev` シェルスクリプトを代わりに使ってください。

# GitHubからインストール

これはエッジやgitツールが好きなMagLevコントリビューターと開発者にとって最も良い方法です。

MagLevのソースツリー等のファイルを保存するために新しいディレクトリを(NFSではなく)ローカルに作成します:

1. MagLevをGitHubリポジトリからcloneして `$MAGLEV_HOME` と `$PATH` を設定します

       git clone git://github.com/MagLev/maglev.git
       cd maglev
       export MAGLEV_HOME=$PWD
       export PATH="$PATH:$MAGLEV_HOME/bin"
       
2. VMをインストール。初めてMagLevをインストールするなら以下を実行してください:

       ./install.sh     # from the maglev directory

   もしくは既に `install.sh` を実行させているならアップグレードスクリプトを実行します:

       ./update.sh     # from the maglev directory

MagLevを使うために `install.sh` を最低1回は実行する必要があります。 `instal.sh`
はMagLevの共有キャッシュなど幾つかの設定を除いて、 `upgrade.sh` が実行する内容を全て含んでいます。

`git pull` した時、MagLevのバージョンの2桁目の数字が変わっているかどうかを
確認してください。もしバージョンが違ったら、バージョン差異が出ないことを保証するために `update.sh` を実行する必要があります。
もしバージョンが違ったら `update.sh` を実行する必要があると表示するのでアップデートのためにバージョンを知る必要はありません。
インストーラー/アップデーターはバージョンを `version.txt` から取得します。

gitの全ての機能は使えますが、 `update.sh` の実行前にcloneを実行しない限り前のバージョンには復元できません。
なぜかというと、インストール/アップグレードスクリプトは永続化のカーネルコードを含むMagLevデータリポジトリの新しいコピーを作成し、そのスクリプトはバックアップを作るのですが、マイグレーションのスクリプトは全く提供されていないからです。


# 特定のMagLevのリリースをインストール

これは現在開発中のバージョンというより、特定のテストされたMagLevのリリースを取ってきます。

1. [MagLev archive](http://glass-downloads.gemstone.com/maglev/) にアクセス
2. 標準的なMagLevリリース-- *例えばMagLev-1.1beta2.tar.gz* かgitアーカイブ -- *例えばMagLev-1.1beta2-git.tar.gz* をダウンロード
3. アーカイブファイルを解凍し、新しくできたディレクトリにcdし、[GitHubからインストール](#install_from_github) このプロセスを `export MAGLEV_HOME=$PWD` ステップから始めてください 


