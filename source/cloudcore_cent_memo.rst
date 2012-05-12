.. CloudCoreにCentOS6.2環境を構築


=================
CloudCore関連メモ
=================

CentOS6.2をインストール
=======================

| 下記URLを参考
| http://aikotobaha.blogspot.jp/2012/03/cloudcore-vps-centos6.html

パッケージ更新
--------------

::

 # まずログイン
 $ yum install man -y
 $ yum update -y

OpenSSHインストール
-------------------
最小構成だとOpenSSHなんか無いからscpとか使えないので

::

 # root
 $ yum install -y openssh-clients


ログインユーザの作成とか
------------------------

::

 # root ログイン
 $ useradd hoge
 $ passwd hoge
 # テケトーにパスワード設定
 $ usermod -g wheel hoge

 # sudo権限を与えるなら
 $ visudo
 # root    ALL=(ALL) ALLの下に
 # hoge    ALL=(ALL) ALL
 # or
 # %wheel  ALL=(ALL) ALL

 # クライアント側で
 $ scp hoge.pub hoge@ipaddressとか:/tmp

 # hoge ログイン
 $ mv /tmp/hoge.pub .ssh
 # authorized_keysがない場合
 $ mv .ssh/hoge.pub .ssh/authorized_keys
 # ある場合は追記
 $ cat .ssh/hoge.pub >> .ssh/authorized_keys
 # パーミッション設定
 $ chmod 700 ~/.ssh
 $ chmod 600 ~/.ssh/authorized_keys

 # クライアント側 .ssh/config
 ServerAliveInterval 10

 Host cloudcore(とか適当な名前)
    HostName ipaddressとか
    Port 22
    User hoge
    IdentityFile ~/.ssh/hoge
 
 # ログインできるか確認
 $ ssh cloudcore

 # hogeユーザでログイン出来ればOK

パスワードログイン/rootログイン禁止
-----------------------------------
| まず以下を行なう
| http://www.atmarkit.co.jp/flinux/rensai/linuxtips/430dnypsswdacces.html

::

 # rootログインも禁止
 # PermitRootLogin no

 # Deprecated option RhostsAuthentication と言われるので
 # - RhostsAuthentication no
 # + RhostsRSAAuthentication no

sudoでPATHなどの環境変数を引き継ぐ
-----------------------------------
| 下記URL参考
| visudo で Defaults:hoge !env_resetを追加したが
| 効果がなかった…
| http://www.pistolfly.jp/weblog/2010/07/sudopath.html

::

  alias sudo='sudo PATH=$PATH'

selinuxの無効化
---------------
| 下記参考
| http://fedorasrv.com/memo/log/35.shtml

::

  $ getenforce
  # Enforcingと返ってくると有効になっている
  $ setenforce 0
  $ vim /etc/sysconfig/selinux
  #SELINUX=enforcing
  SELINUX=disabled


zshに切り替え
--------------
| yum info zsh を見ると versionが古いのでソースコンパイル
| selinux無効化しないとchshで失敗する(なんでかはしらん)

::

  $ yum info zsh
  Loaded plugins: fastestmirror
  Loading mirror speeds from cached hostfile
   * base: ftp.iij.ad.jp
   * extras: ftp.iij.ad.jp
   * updates: ftp.iij.ad.jp
  Available Packages
  Name        : zsh
  Arch        : x86_64
  Version     : 4.3.10
  Release     : 4.1.el6
  Size        : 2.1 M
  Repo        : base
  Summary     : A powerful interactive shell
  URL         : http://zsh.sunsite.dk/
  License     : BSD
  Description : The zsh shell is a command interpreter usable as an interactive
              : login shell and as a shell script command processor.  Zsh
              : resembles the ksh shell (the Korn shell), but includes many
              : enhancements.  Zsh supports command line editing, built-in
              : spelling correction, programmable command completion, shell
              : functions (with autoloading), a history mechanism, and more.


| いろいろとしゃーないのでローカルに落としてから上げる
| http://sourceforge.net/projects/zsh/files/

::

  $ tar zxvf zsh-4.3.17.tar.gz
  $ cd zsh-4.3.17
  # OSインストール直後でなにもないので各種インストール
  $ sudo yum -y install gcc-c++
  $ sudo yum -y install ncurses-devel
  $ sudo yum -y install make
  $ ./configure
  $ make
  $ sudo make install
  # シェルとして登録
  $ which zsh | sudo tee -a /etc/shells
  $ chsh -s $(which zsh)
  # chsh: "/usr/local/bin/zsh" is not executable. とでる場合selinuxを疑ってみて！！


python2.7.3 インストール
------------------------

| ソースからコンパイルする
| 詳細は過去メモ参照しているので、実はビミョー
| pythonbrewを試せばよかったかな…

::

  $ sudo yum install wget
  $ sudo yum install zlib-devel ncurses-devel readline-devel sqlite-devel tcl-devel tk-devel openssl-devel bzip2-devel
  $ wget http://www.python.org/ftp/python/2.7.3/Python-2.7.3.tar.bz2
  $ tar xpvf Python-2.7.3.tar.bz2
  $ ./configure --with-threads --enable-shared
  $ make
  $ sudo make install
  $ sudo sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/python2.7.conf'
  $ sudo /sbin/ldconfig
  $ python -V で2.7.3が出る

eazy_install インストール
--------------------------

::

  $ wget http://peak.telecommunity.com/dist/ez_setup.py
  $ sudo python ez_setup.py

pip インストール
----------------

::

  $ easy_install pip

mercurial インストール
-----------------------

::

  $ pip install mercurial

vim インストール
----------------
ソースからインストールするよ

::

  $ hg clone https://vim.googlecode.com/hg/ vim
  $ cd vim/
  $ ./configure \
  prefix=/usr \
  enable-multibyte \
  enable-mzschemeinterp \
  enable-perlinterp=yes \
  enable-pythoninterp=yes \
  enable-rubyinterp=yes
  $ make
  $ sudo make install

git インストール
----------------

::

  $ yum -y install git-all

.. _nginx-install:

nginx インストール
-------------------
| 下記参照
| http://nginx.org/en/download.html

::

  $ cat << EOS >> /etc/yum.repos.d/nginx.repo
  [nginx]
  name=nginx repo
  baseurl=http://nginx.org/packages/centos/6/\$basearch/
  gpgcheck=0
  enabled=1
  EOS
  $ yum -y install nginx
  $ service nginx start


nginxの現在設定は :ref:`nginx-config`

java インストール
-----------------
せっかくなのでjava7入れる

::

  # http://www.oracle.com/technetwork/java/javase/downloads/jdk-7u4-downloads-1591156.html から
  # jdk-7u4-linux-x64.rpm を取得してscpでupした
  $ rpm -ivh jdk-7u4-linux-x64.rpm

jenkins インストール
--------------------
| 下記参考
| http://pkg.jenkins-ci.org/redhat/

::

  $ sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
  $ sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
  $ sudo yum -y install jenkins
  $ sudo service jenkins start

| jenkinsに関しては :ref:`jenkins-config`
| nginxの proxy pass とかの設定は :ref:`nginx-config`


iptablesの設定
---------------

| CentOSインストール状態でiptablesが設定されている
| 自分の環境にあわせて設定し直す
| 多分初期状態だと上記のjenkinsとか確認できない

::

  以下みたいなのをiptables.shとしてどっかに保存

  =========== ここから ============

  #!/bin/sh
  
  # サービス適応範囲
  trusthost='自分の環境にあわせて' # 下記では使ってないけど
  myhost='自分の環境にあわせて（鯖のip）'
  any='0.0.0.0/0'
  
  # 一旦初期化
  iptables -F
  
  # デフォルトルール
  iptables -P INPUT DROP    # 入ってくるのはダメ。DROP(=deny)はそのまま捨てる。rejectと違い返信しないのでよりセキュアらしい
  iptables -P OUTPUT ACCEPT # 出て行くのはOK
  iptables -P FORWARD DROP  # 通すのはダメ
  
  # 自分からのアクセスはOK
  iptables -A INPUT -i lo -j ACCEPT
  iptables -A OUTPUT -o lo -j ACCEPT
  
  # ICMP
  iptables -A OUTPUT -p icmp --icmp-type echo-request -s $myhost -d $any -j ACCEPT
  iptables -A INPUT -p icmp --icmp-type echo-reply -s $any -d $myhost -j ACCEPT
  
  # 内部から始まった外部からの応答はOK
  iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
  
  # 80 22 は OK
  iptables -A INPUT -p tcp -s $any -d $myhost --dport 80 -j ACCEPT
  iptables -A OUTPUT -p tcp -s $myhost --sport 80 -d $any -j ACCEPT
  
  iptables -A INPUT -p tcp -s $any -d $myhost --dport 22 -j ACCEPT
  iptables -A OUTPUT -p tcp -s $myhost --sport 22 -d $any -j ACCEPT
  
  # DNS
  iptables -A INPUT -p udp --sport 53 -j ACCEPT
  iptables -A INPUT -p udp --dport 53 -j ACCEPT
  iptables -A INPUT -p udp --sport 5353 -j ACCEPT
  iptables -A INPUT -p udp --dport 5353 -j ACCEPT

  =========== ここまで ============

  $ ./iptables.sh # で設定される
  $ service iptables save # saveしないと再起動時に消える


| 上記はどうせ俺しか使わないし〜みたいな適当設定なので、
| なんか問題あっても責任は取りません( ｰ`дｰ´)ｷﾘｯ
| そもそもsshのポートを初期値のまま使っている時点で怖い人に怒られそうｗ

