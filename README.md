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

[出力表示]

![Alt Text](https://github.com/yhidetoshi/Pictures/raw/master/study-httpd/result1.png)


##### バーチャルホストの設定

# mkdir virtual

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

