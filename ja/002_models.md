# アプリケーション開発 1

## Railsアプリケーションの作成

Railsアプリケーションを作成します。ターミナルで以下のコマンドを実行します。

```sh
rails new photolog
cd photolog
```

scaffoldでphotoに関するファイルを作成します。

image、captionというカラムをもたせます。imageには画像ファイルのURLを入れます。ターミナルで以下のコマンドを実行します。

```sh
rails g scaffold photo image:string caption:text
rake db:migrate
```

サーバーを起動して、正しく動作するか確認してみましょう。ターミナルで以下のコマンドを実行します。

```sh
rails s
```

## 課題

* 前回導入したPryを、このRailsアプリケーションにも導入しましょう
* image と caption を必須項目にしましょう
