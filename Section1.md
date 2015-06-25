# Section 1 基本のサーバー構築

##VirtualBoxへのインストール

[CentOSの公式サイト](https://www.centos.org/)からGet CentOS Now →  Minimal ISO(x86_64)のISOファイルをダウンロード。

ViratualBoxを起動して[新規]を選択。
名前やメモリ、容量を設定して起動する。Minimal ISO(x86_64)を選択しインストール完了。

一度、電源オフにしてバーチャルボックス(左上)の[ファイル]タブから[環境設定]→ [ネットワーク]
[ホストオンリーネットワーク]→ 　[ホストオンリーアダプタを追加]をする。

[vbox01]が出来たのを確認する。

###VirtualBoxにインストールしたCentOSの設定

[設定]→ [ネットワーク]→ [ネットワーク]→ [アダプター2]を[ネットワークアダプターを有効化]にチェックを入れて割り当てを[ホストオンリーアダプター]を選択して名前は[vbox01]になっていることを確認。

##ネットワークアダプター1/2へのIPアドレスの設定とssh接続の確認

`/etc/sysconfig/network-script/ifcfg-enp0s3`を編集    
`ONBOOT=yes`に変更し保存

##SSH接続の確認

Ubuntuからsshで仮想マシンに接続できることを確認。(事前にip aで確認しておく)

    ssh ipアドレス

##アップデート

wgetをインストールする。

    sudo yum install wget

###yumやwgetを使用する時のproxyの設定

`/etc/yum.conf`に記述

    proxy=http://172.16.40.1:8888を追加記述。

/etc/wgetrcに記述

    https_proxy = http://172.16.40.1:8888/
    http_proxy = http://172.16.40.1:8888/
    ftp_proxy = http://172.16.40.1:8888/

プロキシの設定が完了したらアップデートが出来ることを確認
    sudo yum update

##1-2 Wordpressを動かす(1)

Apache HTTP Server,MySQL,PHPをインストール

    yum -y install httpd
    yum -y install mariadb-server
    yum -y install php php-mysql php-mbstring php-gd

デーモンを起動

    systemctl start httpd
    systemctl enable httpd
    systemctl stop firewalld

mariadbの設定

    mysql -uroot -p
    create database データベース名;
    grant all privileges on データベース名.* to ユーザー名@localhost identified by 'パスワード';
    exit

WordPressのインストール

    rpm -q php-mysql
    yum -y install php-mysql
    wget http://ja.wordpress.org/wordpress-3.9.1-ja.tar.gz
    tar -xzvf latest.tar.gz
    mv wordpress/ /var/www/html/wpress
    chmod 777 /var/www/html/wpress
    chown -R tu:apache /var/www/html/wpress/
    mkdir /var/www/html/wpress/wp-content/uploads
    mkdir /var/www/html/wpress/wp-content/upgrade
    chmod -R 777 /var/www/html/wpress/wp-content
    rm -f wordpress-3.9.1-ja.tar.gz

SELinuxが有効な場合

    getsebool -a | grep httpl
    setsebool -P httpd_can_network_connect_db 1
    setsebool -P httpd_dbus_avahi 1
    setsebool -P httpd_tty_comm 1
    setsebool -P httpd_unified 1
    yum provides *bin/semanage
    yum -y install policycoreutils-python
    semanage fcontext -a -t httpd_sys_content_t "/var/www/html/データベース名(/.*)?"l
    restorecon -R -v /var/www/html/データベース名
    semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/html/データベース名/wp-content(/.*)?"
    restorecon -R -v /var/www/html/データベース名/wp-content
    setsebool -P allow_ftpd_full_access 1
    systemctl restart httpd

http://サーバー名/wpress/でアクセス出来ることを確認
