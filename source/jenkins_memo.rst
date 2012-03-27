.. jenkins関連のメモ


===============
Jenkins関連メモ
===============

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
| 参考URLのようにjenkinsユーザでsshkeyを作成し、そのキーを登録すればいいのかもしれないが
| 面倒で通常ユーザとキーを共有したのが間違い？


bitbucketからJenkinsに通知
------------------------------------------------
| http://confluence.atlassian.com/display/BITBUCKET/Setting+Up+the+Bitbucket+Jenkins+Service
| リポジトリ -> admin -> Service にてJenkinsを選択し、
| Endpointに Jenkinsのダッシュボードへのリンク
| Project Name にJenkinsでのプロジェクト名
| を入れれば最低限OK


jenkins+Ant+Android
-------------------

動作は未検証
 http://d.hatena.ne.jp/tlync/20120326/1332691894 

参考
----
| http://d.hatena.ne.jp/w650/20110415/1302838728 
| http://d.hatena.ne.jp/tlync/20120326/1332691894 
