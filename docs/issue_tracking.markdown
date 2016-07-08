---
title: Report an Issue with MagLev
layout: default
---
# MagLev Issueレポート

MagLevチームは[GitHubのissue](https://github.com/MagLev/maglev/issues)を使っており、ここでissueを見たり報告したりできます。

To help us better deal with the issue, please include:
より良くissueに対処して私たちを助けるには、以下を含めて下さい:

1. MagLevのバージョン

       $ maglev-ruby -v
       maglev 1.0.0 (ruby 1.8.7) (2011-10-31 rev 1.0.0-27184)[Darwin i386]

2. MagLevが実行されているシステムの情報:

       $ uname -a
       Darwin miami.local 10.8.0 Darwin Kernel Version 10.8.0: Tue Jun  7 16:33:36 PDT 2011; root:xnu-1504.15.3~1/RELEASE_I386 i386


3. RVMを使ってインストールしたかGitHubからインストールしたか

## Stack Traces

If possible, a stack trace of where the issue occurs is always nice to
もし可能なら、スタックトレースが問題が起きた時に便利です。
have.

スタックトレースを作成するには:

1. MagLevをデバッグオプションを付けて起動してください

       $ export MAGLEV_OPTS="-d"
       $ maglev-ruby the_script_that_breaks.rb

2. エラー発生時、MagLevはデバッガーで止まり `topaz 1> ` プロンプトが表示されます:

```
       error , a ZeroDivide occurred (error 2026), reason:numErrIntDivisionByZero, attempt to divide 1 by zero,
                    during /Users/pmclain/GemStone/checkouts/the_script_that_breaks.rb
       ERROR 2026 , a ZeroDivide occurred (error 2026), reason:numErrIntDivisionByZero, attempt to divide 1 by zero (ZeroDivisionError)
       topaz 1> exitifnoerror
       End of initialization files
       topaz 1>
```

3. デバッガーにスタックトレースを表示させる

   まずtopazに `output push <some_file_name>` で出力を保存するようにさせ、
   `stack` コマンドでスタックトレース表示します:

```
       topaz 1> output push maglev_stack.txt
       topaz 1> stack
       ==> 1 AbstractException >> _outer:with:        (envId 0) @8 line 19
           receiver [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
           aHandler nil
           isNonStaticAnsi nil
       2 AbstractException >> outer               (envId 0) @2 line 19
           receiver [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
       3 [] in  RubyCompiler >> compileFileNamed:loadName:env: (envId 0) @4 line 58
           self [114898433  RubyCompiler] aRubyCompiler
           receiver [114898945 sz:0  ExecBlock1] anExecBlock1
           ex [114893057 sz:10  ZeroDivide] a ZeroDivide occurred (error 2026), reason:numErrIntDivision...
           prevSelf nil

       ...more stack trace...
```

4. MagLev VMを終了するために exit と入力する:


       topaz 1> exit

   スタックトレースは `maglev_stack.txt` (もしくは適当に付けた名前のファイル)に保存されます。

