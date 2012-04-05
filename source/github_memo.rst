.. github関連のメモ


===============
github関連メモ
===============

ssh -i ぽいことをやる
----------------------

mercurial の場合、.hgrc にテキトーにコマンド書いたりすればいいのだが、gitはそうは行かなかった

.hgrc だったらこんな感じ
::

 [ui]
 ssh = -C -i ~/.ssh/hoge_key

git の場合、GIT_SSHに通知しなければならないらしくこうなる


| まず下記のようなシェルスクリプトを用意
| ここでは/usr/local/bin/git_ssh_wrap にあると仮定

::

 #!/bin/sh
 exec ssh -i ~/.ssh/hoge_key "$@"

以下のように使用

::

 $ cd /tmp #適当なディレクトリへ
 $ GIT_SSH=git_ssh_wrap git clone git@github... #とか

まいどGIT_SSH=... とかダルイのでaliasでも

::

 $ alias git-wrap GIT_SSH=git_ssh_wrap git
 $ git-wrap clone git@github...

| 接続先別等でこれするなら ~/.ssh/config にサーバ名と使用するkeyを設定すればいいと思うが
| なぜかうまく行かないことがあり、結局この方法に落ち着いていたりいなかったり


参考
----
| http://hisashim.org/2010/02/23/git-ssh-options.html
