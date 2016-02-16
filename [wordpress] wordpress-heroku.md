# HerokuでWordPressをすぐ動かせるテンプレートを作った

https://github.com/shimoju/wordpress-heroku

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/shimoju/wordpress-heroku)


## WordPressでブログを作る上でやりたいこと

- サイト全体をGit管理する
- Gitでデプロイする
- WordPress本体をGitリポジトリに入れたくない
- Heroku readyな構成

WordPress本体をリポジトリに入れないようにするには、

- git submodule
- composer

の2つの方法があるが、composerの方が現代的で便利そう

composer参考：
http://tech.quartetcom.co.jp/2014/08/04/371
https://github.com/qckanemoto/WordPress.Skeleton

## テンプレート
https://github.com/shimoju/wordpress-heroku

- ComposerでWordPressをインストール
- WordPress PackagistでプラグインもComposerで管理
- Heroku対応済み
  - 設定は環境変数から取得する
  - HHVM使用
  - Procfile設定済み

### TODO
- プラグイン選定
- ファイルアップロード
- メール(sendgrid)
- CDNとキャッシュ

- README
- bin/スクリプトをより機能的にする


## post-install-cmd
`ln: wp/wp-content/my-themes: File exists`
`ln: wp/wp-content/mu-plugins: File exists`

`ln: wp/wp-content/my-themes: No such file or directory`と言われるだけ
