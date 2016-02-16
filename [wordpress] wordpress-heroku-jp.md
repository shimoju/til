# wordpress-heroku 日本語README

[Heroku](https://www.heroku.com/)で[WordPress](https://ja.wordpress.org/)をすぐ動かせるテンプレート

https://github.com/shimoju/wordpress-heroku

## セットアップ

Gitでプロジェクトをcloneする。

```
git clone https://github.com/shimoju/wordpress-heroku.git your-blog-name
cd your-blog-name
```

`composer install`でWordPressをインストールする。

```
composer install --ignore-platform-reqs
```

[HerokuでHHVMを使う設定](https://devcenter.heroku.com/articles/php-support#selecting-a-runtime-hhvm)にしているため、HHVMで実行されていないとエラーが出る。`--ignore-platform-reqs`をつけることでこのエラーを無視できる。


ローカル用設定ファイルをコピーし、`.htaccess`も作成しておく。

```
cp local-config-sample.php local-config.php
touch wp/.htaccess && chmod 666 wp/.htaccess
```

### local-config.phpを編集する

MySQLの設定、


### WordPressをインストールする




## Herokuにデプロイする

herokuでアプリを作成する。

```
heroku create your-blog-name
```

HerokuのMySQLアドオン、ClearDBを追加する。

```
heroku addons:add cleardb
```

セットアップスクリプトを使って

```
bin/db_url
bin/secret_key
```

`heroku config:get CLEARDB_DATABASE_URL`を実行し、表示されたURLから`DB_NAME`などを設定する。

Herokuにpushする。

```
git push heroku master
```

### WordPressをインストールする

```
heroku open
```

で

## 設定

### ステージング環境を設定する

```
heroku config:set WP_ENV=staging
```

### GitHub OAuth tokenを設定する

[あ](https://devcenter.heroku.com/articles/php-support#custom-github-oauth-tokens)

```
heroku config:set COMPOSER_GITHUB_OAUTH_TOKEN=your_token
```

### NginxではなくApacheを使う

`Procfile`に以下を記述

```
web: vendor/bin/heroku-hhvm-apache2 -C config/apache.conf wp/
```

## ライセンス

[GNU GPL v2.0](https://github.com/shimoju/wordpress-heroku/blob/master/LICENSE)

## 作者

[Hiroshi Shimoju](https://github.com/shimoju)
