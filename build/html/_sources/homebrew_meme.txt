.. homebrew関連のメモ

.. _homebrew-memo:

=======================
HOMEBREW関連メモ
=======================

brew updateで失敗する
------------------------

::

  $ brew update                                                                                                                  [/usr/local/Library/Formula]
  Library/Formula/sbt.rb: needs merge
  error: you need to resolve your current index first
  Error: Failure while executing: git checkout -q master

| 自前でhomebrewのgitレポジトリを弄っている場合にあったりなかったり
| 特にバージョン指定とかしていると起こる

::

  $ cd /usr/local/Library/Formula
  $ git status (で状況確認)
  なにかする git stash とかで一時的に見なかったことにだいたいする
  $ brew update とか実行
  $ git stash apply とかでもとに戻す

あたりで適当に解消




