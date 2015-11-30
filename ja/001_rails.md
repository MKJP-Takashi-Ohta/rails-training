# Ruby on Rails 基礎

## Railsアプリケーションの作成

Railsアプリケーションを作成します。

```
rails new first-app
```

Railsアプリケーションは以下のような構成になっています。

```
first-app
├── Gemfile # ライブラリを管理するファイル
├── Gemfile.lock
├── README.rdoc
├── Rakefile
├── app # アプリケーションのコードが配置される
│   ├── assets
│   │   ├── images
│   │   ├── javascripts
│   │   │   └── application.js
│   │   └── stylesheets
│   │       └── application.css
│   ├── controllers
│   │   ├── application_controller.rb
│   │   └── concerns
│   ├── helpers
│   │   └── application_helper.rb
│   ├── mailers
│   ├── models
│   │   └── concerns
│   └── views
│       └── layouts
│           └── application.html.erb
├── bin
│   ├── bundle
│   ├── rails
│   ├── rake
│   ├── setup
│   └── spring
├── config # アプリケーションの設定ファイル
│   ├── application.rb
│   ├── boot.rb
│   ├── database.yml
│   ├── environment.rb
│   ├── environments
│   │   ├── development.rb
│   │   ├── production.rb
│   │   └── test.rb
│   ├── initializers
│   │   ├── assets.rb
│   │   ├── backtrace_silencers.rb
│   │   ├── cookies_serializer.rb
│   │   ├── filter_parameter_logging.rb
│   │   ├── inflections.rb
│   │   ├── mime_types.rb
│   │   ├── session_store.rb
│   │   └── wrap_parameters.rb
│   ├── locales
│   │   └── en.yml
│   ├── routes.rb
│   └── secrets.yml
├── config.ru
├── db
│   └── seeds.rb
├── lib
│   ├── assets
│   └── tasks
├── log
├── public # 静的なファイル
│   ├── 404.html
│   ├── 422.html
│   ├── 500.html
│   ├── favicon.ico
│   └── robots.txt
├── test
│   ├── controllers
│   ├── fixtures
│   ├── helpers
│   ├── integration
│   ├── mailers
│   ├── models
│   └── test_helper.rb
├── tmp
│   └── cache
│       └── assets
└── vendor
    └── assets
        ├── javascripts
        └── stylesheets
```

Railsアプリケーションを起動します。

```
cd first-app
rails server
```

## scaffoldによるコードの自動生成

Railsではさまざまなコードを自動生成できます。scaffoldでアプリケーションの雛形を作ります。
scaffoldは一覧、詳細、追加、削除、変更のコードを自動生成します。

ここでは **bookmark** を管理する機能を作ります。

```
rails generate scaffold bookmark title:string description:text url:string
```

DBを更新して、アプリケーションを起動します。

```
bin/rake db:migrate
rails server
```

[http://localhost:3000/bookmarks](http://localhost:3000/bookmarks)で動作確認ができます。

データの追加、削除、変更などを行ってみましょう。


## scaffoldで生成されるファイル

scaffoldで生成されたファイルを確認していきます。scaffoldではこのような実行結果が出力されます。

```
% rails generate scaffold bookmark title:string description:text url:string
      invoke  active_record
      create    db/migrate/20151129091144_create_bookmarks.rb
      create    app/models/bookmark.rb
      invoke    test_unit
      create      test/models/bookmark_test.rb
      create      test/fixtures/bookmarks.yml
      invoke  resource_route
       route    resources :bookmarks
      invoke  scaffold_controller
      create    app/controllers/bookmarks_controller.rb
      invoke    erb
      create      app/views/bookmarks
      create      app/views/bookmarks/index.html.erb
      create      app/views/bookmarks/edit.html.erb
      create      app/views/bookmarks/show.html.erb
      create      app/views/bookmarks/new.html.erb
      create      app/views/bookmarks/_form.html.erb
      invoke    test_unit
      create      test/controllers/bookmarks_controller_test.rb
      invoke    helper
      create      app/helpers/bookmarks_helper.rb
      invoke      test_unit
      invoke    jbuilder
      create      app/views/bookmarks/index.json.jbuilder
      create      app/views/bookmarks/show.json.jbuilder
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/bookmarks.coffee
      invoke    scss
      create      app/assets/stylesheets/bookmarks.scss
      invoke  scss
      create    app/assets/stylesheets/scaffolds.scss
```

scaffoldでは以下のようなファイルを生成しています。

* active_record
  * テーブルの作成
  * モデルの作成
* routeの設定
* controller
  * viewの作成
  * helperの作成
  * jbuilder(jsonテンプレート)の作成
* assets
  * CoffeeScriptの作成
  * Scssの作成

実際にファイルを開いて確認して見ましょう。

## 課題

* link_toを使ってURLをリンクにしてみましょう。
* URLにバリデーションを掛けましょう
* タイトルとURLを必須項目にしましょう。
