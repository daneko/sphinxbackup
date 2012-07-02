.. playframework関連のメモ

==============
Play!関連メモ
==============

play2.0.1 で test
------------------


sbtでもplayコマンドでもテストは実行可能

::

  play new hoge
  cd hoge
  # 作業
  sbt clean update compile test
  or
  play test


| sbtの場合、まず依存関係の解消に向かうため、totalでは遅い
| playコマンドの場合（おそらくsbtをラップしているので）、test自体は遅い
| ローカルで実行するさいはどっちでもいいと思うけど
| インタラクティブモードにして ~test で実行すればいいと思う
| 
| 使い方次第だけどJenkinsのplayプラグインは1系が対象？
| → Play 1.1 or later とはあるけど 未確認
| https://wiki.jenkins-ci.org/display/JENKINS/play-plugin
|
| なお、play2.0.1はsbt0.11.2を使用しているので
| **sbt で play アプリケーションをテストしたりする場合は**
| sbt0.11.2をインストールしている状態にすること
| → sbt0.11.3 だとそもそもダメだった
| → playコマンドの場合sbt内包っぽいので問題ない
| 
| mac で homebrewを使用している場合は下記のように11.2を指定してインストールし直すこと

::

  brew uninstall sbt
  brew versions sbt
  # 11.2のgitコマンドをコピー
  cd /usr/local
  git checkout...
  brew install sbt

play2.0.2だとsbt0.11.3でもOKぽい


play2.0.2 with mongodb
------------------------

| https://github.com/leon/play-salat
| あたりのプラグインを使うとできる
| 
| https://github.com/Mironor/Play-2.0-Scala-MongoDb-Salat-exemple
| 当たりが参考になる


