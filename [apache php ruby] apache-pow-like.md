# Apacheを一瞬でWebアプリが動かせるPowっぽい開発サーバにする

今までRubyアプリの開発用サーバには[Pow](http://pow.cx/)を使ってきた。設定無しですぐアプリが動いて超楽なのだが、Linuxでは使えないし最近PHPも触っていたりするので、普通のWebサーバでまとめてRubyもPHPも扱いたくなってきた。

Powのように、あるディレクトリにシンボリックリンクするとすぐアプリが動かせることを目指した。
結果、このような設定になった：https://github.com/shimoju/dotwebapps

## 必要なもの

- Apache（2.4）
- PHPアプリ用：PHP + PHP-FPM
- Ruby（など）アプリ用：[Phusion Passenger](https://www.phusionpassenger.com/)

以下のApacheモジュールを有効にしておく：

- [mod_proxy](http://httpd.apache.org/docs/current/mod/mod_proxy.html),
- [mod_proxy_fcgi](http://httpd.apache.org/docs/current/mod/mod_proxy_fcgi.html)：FastCGIにプロキシする
- [mod_vhost_alias](http://httpd.apache.org/docs/current/mod/mod_vhost_alias.html)：
DocumentRootの動的設定（VirtualDocumentRoot）
- [mod_passenger](https://www.phusionpassenger.com/documentation/Users%20guide%20Apache.html)：Phusion Passenger

その他：

- Powが既に入っているなら[アンインストールする](http://pow.cx/manual.html#section_1.2)
- Rubyはrbenvでインストールしておく
- HomebrewでApache・PHPをインストールするなら[こちら](http://qiita.com/shimoju/items/6710d68b0bc08e2cb55f)を参照
- PassengerはHomebrewで入れるのが簡単：`brew install passenger`
- Apacheは[localhostの80番ポート](http://localhost/)で起動していることを前提にする

## 開発用ドメインを用意する

`/etc/hosts`にドメインをいちいち書いていくのは苦行なので、便利にするために開発用ドメインを用意する。2つの方法がある。

1. ローカルにDNSサーバーを立てる（dnsmasqなど）
2. 普通のドメインを`127.0.0.1`に名前解決させる

ローカルにdnsmasqを立てる方法は、[osxでプロダクションに限りなく近い開発環境を準備する](http://qiita.com/geta6/items/8faacf8efcf70e74875a)を参照。

普通のドメインを127.0.0.1に名前解決させるには、[127.0.0.1に名前解決するループバックドメインloopback.jpを作った](http://qiita.com/shimoju/items/81ed5055d2fec5bb9c1e)を参照。

このように設定されたドメインが既にあるので、管理者が信頼できるのであればそちらを使ってもよい。（`lvh.me`や`127.0.0.1.xip.io`など）

今回は2.の方法で自作した`loopback.jp`を使う。

## 設定
 
必要なものをインストールしてモジュールを有効化したら、設定ファイルをcloneする。`httpd.conf`でメイン設定ファイルをIncludeする。

```
git clone https://github.com/shimoju/dotwebapps.git ~/.web
echo "Include \"${HOME}/.web/config/apache/dotwebapps.conf\"" >> /usr/local/etc/apache2/2.4/httpd.conf
```


```apache:httpd.conf
vim /usr/local/etc/apache2/2.4/httpd.conf

# コメントを外す
LoadModule proxy_module libexec/mod_proxy.so
LoadModule proxy_fcgi_module libexec/mod_proxy_fcgi.so
LoadModule vhost_alias_module libexec/mod_vhost_alias.so
```


```
LoadModule passenger_module /usr/local/opt/passenger/libexec/buildout/apache2/mod_passenger.so

<IfModule mod_passenger.c>
PassengerRoot /usr/local/opt/passenger/libexec/lib/phusion_passenger/locations.ini
PassengerDefaultRuby /usr/bin/ruby
</IfModule>
```

TODO: 個別設定

設定したら

```
apachectl configtest
```

で設定ファイルをチェックし、`Syntax OK`ならApacheを再起動する。

```
sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist && sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
```

## デプロイ

Rubyアプリなら`~/.web/apps/ruby`、
PHPアプリなら`~/.web/apps/php`にシンボリックリンクする。

例えばDropboxにプロジェクトを置いているとすると、

```
chmod 755 ~/Dropbox
ln -s ~/Dropbox/ruby-app ~/.web/apps/ruby
ln -s ~/Dropbox/php-app ~/.web/apps/php
```

となる。

TODO:パーミッションについて


## アクセス
