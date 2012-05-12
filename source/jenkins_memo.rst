.. jenkins関連のメモ

.. _jenkins-config:

================
Jenkins関連メモ
================

jenkinsでMercurial+bitbucketを使うために
-----------------------------------------

mercurial plugin を導入する

| 現状不明点
| Jenkinsでビルド実行時にまずcloneするはずだが、認証に失敗する
| そのため下記構成を先ずは作成した

::

  JENKINS_HOME
  └── .ssh/id_rsa
  └── .hgrc

.hgrcの中身

::

  [ui]
  ssh = ssh -C -i path_to_JENKINSHOME/.ssh/id_rsa

| いずれも
| sudo -u jenkins
| で行なった or 最後に保有者をjenkinsUserに置き換えた
| 
| この状態でビルド実行してもダメだったが
| sudo -u jenkins hg clone ssh://hg@bitbucket.org/...
| を適当なところで試し、再度ビルド実行を行ったところ、hg cloneが成功した
| 
| おそらくフィンガープリントが登録されていなかったことが原因^^;
|


bitbucketからJenkinsに通知
------------------------------------------------
| http://confluence.atlassian.com/display/BITBUCKET/Setting+Up+the+Bitbucket+Jenkins+Service
| リポジトリ -> admin -> Service にてJenkinsを選択し、
| Endpointに Jenkinsのダッシュボードへのリンク
| Project Name にJenkinsでのプロジェクト名
| を入れれば最低限OK

bitbucketの通知を妨げない最低限のJenkins上でのセキュリティ
----------------------------------------------------------

.. blockdiag::
   :desctable:

   blockdiag {
    default_fontsize = 16
    node_width = 100;
    span_width = 120;  // default value is 64
    span_height = 120;  // default value is 40

    // set default shape
    default_shape = roundedbox;  // default value is 'box'
    A -> B [label = "push", fontsize=14];
    B -> C [label = "build通知", fontsize=14];
    A[description = "ローカル（開発環境）"];
    B[description = "Bitbucket とか"];
    C[description = "Jenkins"];
   }

上記のような環境で、JenkinsにBuild通知を行おうとすると下記権限が必要っぽい

* 全体:Read
* ジョブ:Read/Build

| これを匿名ユーザに許可するわけだが、そうすると誰でもBuildが実行できてしまうし、
| 成果物のDLも可能な状態となる。
| WebAPIの使用自体は各jobの設定からTokenをつけることである程度回避できそうだが、
| 結局直接見にこられる分には回避ができない。
| 純粋に下記構成にすれば匿名ユーザから権限を外すことも可能だけどなんかスマートじゃない

.. blockdiag::

   blockdiag {
    default_fontsize = 16
    node_width = 100;
    span_width = 120;  // default value is 64
    span_height = 120;  // default value is 40

    // set default shape
    default_shape = roundedbox;  // default value is 'box'
    A -> B [label = "push", fontsize=14];
    B <- C [label = "polling", fontsize=14];
   }



jenkins + git + github
----------------------

github plugin を入れる

| 入れようとするとgit関連のプラグインが勝手に複数インストールされるので入れてもらう
| プラグイン側からgithub側に設定を飛ばせる
| なのでアカウントだけ渡せば、bitbucketと異なりjenkins側だけで操作が完了

jenkins + sbt
-------------

sbt plugin を入れる

| 必要なのはsbt-launcher「.jar」の場所を指定すること
| その他job毎にsbt Flagを指定することとなる
| actionはsbtで実行したいコマンドをただ書くだけ
| 自分でsbtを実行するのと何らかわりなし


jenkins+Ant+Android
-------------------

| 動作は未検証
| http://d.hatena.ne.jp/tlync/20120326/1332691894
| https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin
| http://mattari.sumomo.ne.jp/2011/10/jenkins-%E3%81%A7-android-unittest-%E3%82%92%E8%87%AA%E5%8B%95%E5%AE%9F%E8%A1%8C%E3%81%97%E3%81%A6%E3%82%AB%E3%83%90%E3%83%AC%E3%83%83%E3%82%B8%E3%82%92%E6%8E%A1%E5%8F%96%E3%81%99%E3%82%8B%E3%80%82/

参考
----
| http://d.hatena.ne.jp/w650/20110415/1302838728 
| http://d.hatena.ne.jp/tlync/20120326/1332691894 
