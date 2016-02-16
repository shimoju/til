# Cordova jp 6/19

## Angular

入門には小さいフレームワークの方が→Backbone,knockout
開眼JS

P/F/Sは全部サービスを作るため

View
- テンプレート：生HTML
- ディレクティブ：テンプレート中で呼び出す機能
  - ex) Onsen UIの機能

- DI
- Module→簡単アプリなら1つで

モジュール
第2引数で依存モジュールを取り込み
テンプレートでモジュールをロード：ng-app
→ons.bootstrapでやっている

ディレクティブ
- ng-** がディレクティブ

エクスプレッション
{{name}}で展開：バインディング

コントローラー
コントローラーに変数をもたせる：this.foo
ng-controllerで呼び出し：MainController as Main←asは1.2から
ng-modelによるバインディング

スコープによる値共有→$scope 整理したい場合はよくない
今は公式デモでもasを使ってる

DI：依存するオブジェクトを外部から取得

コントローラー間での共有：サービスを作成
Provider/Factory/Service

app.factory：中でオブジェクトを作ってreturn
Factoryでオブジェクトを1つ作り、コントローラー間で共有

Factoryで作り、コントローラーで取り込み

## Cordovaプラグインの作り方

Crosswalk：Chromiumベースのブラウザをアプリに搭載
Android 内蔵ブラウザ重い・挙動違いを改善
cordova5.0からプラグイン化：cordova plugin add ~crosswalk-webview
Androidデバッグ：chrome://inspect/#devices

Cordovaプラグイン：ネイティブ機能を組み込むもの
ex)地図とかバックグラウンド処理とか

- 設定：plugin.xml
- 共通部分（橋渡し）：www/~.js
- OS固有ファイル：src/ios|android

Android：CordovaPluginクラスを継承して作成
Javaで処理記述→JSに戻す

流れ
plugin.xmlのplatform設定にプラグイン情報を記述：ヘッダファイルのパスなど
src/ios/ObjCファイルを作成
プラグインテンプレートをimport→O-Cで処理記述→JSにcallback

## プラグインをもっと簡単に
プラグイン開発：覚えることたくさん
Plugman（公式） だけどコマンド長い つらい
→Yeoman generator作ってみた

https://github.com/sony/generator-cordova-plugin-devbed

TestBedアプリ：Jasmineテストランナー用アプリも自動作成
テストも完璧だね！！！

```
cd Plugin_TestBed
cordova run android
```

ブリッジJS編集：/plugin.js
プラグイン編集：src/android
バインディングし、Javaで内容記述

/testsもプラグイン→テスト記述

## SNSアプリ

BCPortal：企業向けの防災用ポータルサイト構築サービス
SNSクライアントアプリをCordovaで実装
Why? Webアプリ実装の活用
jQMのパフォーマンスチューニングつらい問題
→新機能実装でOnsen UI + Angular使用

ハマりポイント
- プラグインの更新重要
- ソフトウェアキーボードの挙動怪しい
ionic keyboardプラグインよさげ？

## CordovaとElectron

Electron：Nodeベースのハイブリッドアプリ実行環境
主にデスクトップアプリ
