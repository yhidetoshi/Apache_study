### Apacheの学習


- 環境
  - Vagrant
    - CentOS6.7
  

- インストール
  - `yum -y install httpd`
  - `rpm -qa | grep httpd`
```
# rpm -qa | grep httpd
httpd-tools-2.2.15-53.el6.centos.x86_64
httpd-2.2.15-53.el6.centos.x86_64
```

- ディレクトリ確認
```
# tree
.
|-- conf
|   |-- httpd.conf
|   `-- magic
|-- conf.d
|   |-- README
|   `-- welcome.conf
|-- logs -> ../../var/log/httpd
|-- modules -> ../../usr/lib64/httpd/modules
`-- run -> ../../var/run/httpd
```

`vim /etc/httpd/conf/httpd.conf`

いくつかピックアップ. httpdの全体の設定はこのファイルでしてるみたい
```
Include conf.d/*.conf
User apache
Group apache
ServerAdmin root@localhost
DocumentRoot "/var/www/html"
LogLevel warn
AddDefaultCharset UTF-8
```

- 適当な表示用ファイルを作成
  - `/var/www/html/hoge.txt`
  - httpdが参照できるように権限付与
# cat hoge.html
```
<html>
  <p> hogehuga</p>
</html>
```

#####バーチャルホストの設定

`#cd /var/www/html`
`# mkdir virtual`

**バーチャルホストで利用する設定(切り替え)**
/etc/httpd/conf/httpd.conf
```
#ServerName centossrv.com:80　←　行頭に#を追加してコメントアウト
# Use name-based virtual hosting.
#
NameVirtualHost *:80　←　コメント解除(バーチャルホスト有効化)
```

`#vi /etc/httpd/conf.d/virtualhost-00.conf`

```
※バーチャルホスト未定義ホスト名でアクセス時にアクセスを拒否する
<VirtualHost *:80>
    ServerName any
    <Location />
        Order deny,allow
        Deny from all
    </Location>
</VirtualHost>
```

`# vi /etc/httpd/conf.d/virtualhost-centossrv.com.conf`

-> メインホスト用バーチャルホスト設定ファイル作成
```
<VirtualHost *:80>
    ServerName centossrv.com
    DocumentRoot /var/www/html
</VirtualHost>
```

`# vi /etc/httpd/conf.d/virtualhost-virtual.com.conf`　

-> 追加ホスト用バーチャルホスト設定ファイル作成
```
<VirtualHost *:80>
    ServerName virtual.com
    DocumentRoot /var/www/html/virtual
    ErrorLog logs/virtual-error_log
    CustomLog logs/virtual-access_log combined env=!no_log
</VirtualHost>
```

バーチャルホストの設定ができればhttpdを再起動する


[テスト結果1]

`# curl centossrv/hoge.html`
```
<html>
   <p> hogehuga</p>
</html>
```

[テスト結果2]

`# curl virtual/hoge.html`
```
<html>
   <p> virtalhost-test</p>
</html>
```

[ログ出力の確認]
```
# pwd
/var/log/httpd
[root@chef-client1 httpd]# ls
access_log  error_log  virtual-access_log  virtual-error_log
```
