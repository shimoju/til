# XAMPPで動的にバーチャルホストを設定する
バージョン：XAMPP for Mac 5.6.3 (xampp-osx-5.6.3-0)


## ポートを変更する
Powが既に80番ポートを使っているので、Apacheは8080番ポートに変更する。([共存させる方法もある](https://github.com/basecamp/pow/wiki/Running-Pow-with-Apache)が、XAMPPは既存環境と完全に分離されているのが利点なのでPow側の設定はいじりたくなかった)

`/Applications/XAMPP/xamppfiles/etc/httpd.conf`：52行目付近

```
Listen 8080
```


## バーチャルホストを有効にする
`#Include etc/extra/httpd-vhosts.conf`のコメントを外し、`httpd-vhosts.conf`が読み込まれるようにする

`/Applications/XAMPP/xamppfiles/etc/httpd.conf`：488行目付近

```
# Virtual hosts
Include etc/extra/httpd-vhosts.conf
```

### VirtualDocumentRootを使って動的にバーチャルホストを設定する
バーチャルホストの設定は`/Applications/XAMPP/xamppfiles/etc/extra/httpd-vhosts.conf`に記述する。
`mod_vhost_alias`の`VirtualDocumentRoot`機能を使うことで、同じようなVirtualHostエントリをいちいち書かなくてもよくなる。

- サンプルの設定は削除する
- 80番ポートのままの場合は`<VirtualHost *:80>`にする
- VirtualHostを有効にするとデフォルトのlocalhostでアクセスできなくなるので、最初にデフォルトの設定をし直す
- 2個目のVirtualHostでVirtualDocumentRootを設定

```
# Default
<VirtualHost *:8080>
    ServerName localhost
    DocumentRoot "/Applications/XAMPP/xamppfiles/htdocs"
</VirtualHost>

# VirtualDocumentRoot
<VirtualHost *:8080>
    ServerName loopback.jp
    ServerAlias *.loopback.jp
    VirtualDocumentRoot "/Applications/XAMPP/xamppfiles/htdocs/vhosts/%1"
</VirtualHost>
```

あとは

```
mkdir /Applications/XAMPP/xamppfiles/htdocs/vhosts
```

して、
