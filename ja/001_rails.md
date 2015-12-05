# Ruby on Rails 基礎

## Railsアプリケーションの作成

Railsアプリケーションを作成します。

```sh
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

```sh
cd first-app
rails server
```

## scaffoldによるコードの自動生成

Railsではさまざまなコードを自動生成できます。scaffoldでアプリケーションの雛形を作ります。
scaffoldは一覧、詳細、追加、削除、変更のコードを自動生成します。

ここでは **bookmark** を管理する機能を作ります。

```sh
rails generate scaffold bookmark title:string description:text url:string
```

DBを更新して、アプリケーションを起動します。

```sh
bin/rake db:migrate
rails server
```

[http://localhost:3000/bookmarks](http://localhost:3000/bookmarks)で動作確認ができます。

データの追加、削除、変更などを行ってみましょう。


## scaffoldで生成されるファイル

scaffoldで生成されたファイルを確認していきます。scaffoldではこのような実行結果が出力されます。

```sh
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

### migration

db/migrate/20151129091144_create_bookmarks.rb

```ruby
class CreateBookmarks < ActiveRecord::Migration
  def change
    create_table :bookmarks do |t|
      t.string :title
      t.text :description
      t.string :url

      t.timestamps null: false
    end
  end
end
```

### routes

config/routes.rb

```ruby
Rails.application.routes.draw do
  resources :bookmarks
  # The priority is based upon order of creation: first created -> highest priority.
  # See how all your routes lay out with "rake routes".

  # You can have the root of your site routed with "root"
  # root 'welcome#index'

  # Example of regular route:
  #   get 'products/:id' => 'catalog#view'

  # Example of named route that can be invoked with purchase_url(id: product.id)
  #   get 'products/:id/purchase' => 'catalog#purchase', as: :purchase

  # Example resource route (maps HTTP verbs to controller actions automatically):
  #   resources :products

  # Example resource route with options:
  #   resources :products do
  #     member do
  #       get 'short'
  #       post 'toggle'
  #     end
  #
  #     collection do
  #       get 'sold'
  #     end
  #   end

  # Example resource route with sub-resources:
  #   resources :products do
  #     resources :comments, :sales
  #     resource :seller
  #   end

  # Example resource route with more complex sub-resources:
  #   resources :products do
  #     resources :comments
  #     resources :sales do
  #       get 'recent', on: :collection
  #     end
  #   end

  # Example resource route with concerns:
  #   concern :toggleable do
  #     post 'toggle'
  #   end
  #   resources :posts, concerns: :toggleable
  #   resources :photos, concerns: :toggleable

  # Example resource route within a namespace:
  #   namespace :admin do
  #     # Directs /admin/products/* to Admin::ProductsController
  #     # (app/controllers/admin/products_controller.rb)
  #     resources :products
  #   end
end
```

```rake routes``` で定義されいているroutesを確認できます。

```sh
% rake routes
       Prefix Verb   URI Pattern                   Controller#Action
    bookmarks GET    /bookmarks(.:format)          bookmarks#index
              POST   /bookmarks(.:format)          bookmarks#create
 new_bookmark GET    /bookmarks/new(.:format)      bookmarks#new
edit_bookmark GET    /bookmarks/:id/edit(.:format) bookmarks#edit
     bookmark GET    /bookmarks/:id(.:format)      bookmarks#show
              PATCH  /bookmarks/:id(.:format)      bookmarks#update
              PUT    /bookmarks/:id(.:format)      bookmarks#update
              DELETE /bookmarks/:id(.:format)      bookmarks#destroy
```

### models

app/models/bookmark.rb

```ruby
class Bookmark < ActiveRecord::Base
end
```

### controllers

app/controllers/bookmarks_controller.rb

```ruby
class BookmarksController < ApplicationController
  before_action :set_bookmark, only: [:show, :edit, :update, :destroy]

  # GET /bookmarks
  # GET /bookmarks.json
  def index
    @bookmarks = Bookmark.all
  end

  # GET /bookmarks/1
  # GET /bookmarks/1.json
  def show
  end

  # GET /bookmarks/new
  def new
    @bookmark = Bookmark.new
  end

  # GET /bookmarks/1/edit
  def edit
  end

  # POST /bookmarks
  # POST /bookmarks.json
  def create
    @bookmark = Bookmark.new(bookmark_params)

    respond_to do |format|
      if @bookmark.save
        format.html { redirect_to @bookmark, notice: 'Bookmark was successfully created.' }
        format.json { render :show, status: :created, location: @bookmark }
      else
        format.html { render :new }
        format.json { render json: @bookmark.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /bookmarks/1
  # PATCH/PUT /bookmarks/1.json
  def update
    respond_to do |format|
      if @bookmark.update(bookmark_params)
        format.html { redirect_to @bookmark, notice: 'Bookmark was successfully updated.' }
        format.json { render :show, status: :ok, location: @bookmark }
      else
        format.html { render :edit }
        format.json { render json: @bookmark.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /bookmarks/1
  # DELETE /bookmarks/1.json
  def destroy
    @bookmark.destroy
    respond_to do |format|
      format.html { redirect_to bookmarks_url, notice: 'Bookmark was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_bookmark
      @bookmark = Bookmark.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def bookmark_params
      params.require(:bookmark).permit(:title, :description, :url)
    end
end
```

### helpers

app/helpers/bookmarks_helper.rb

```
module BookmarksHelper
end
```

### views

app/views/bookmarks/index.html.erb

```html
<p id="notice"><%= notice %></p>

<h1>Listing Bookmarks</h1>

<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Description</th>
      <th>Url</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @bookmarks.each do |bookmark| %>
      <tr>
        <td><%= bookmark.title %></td>
        <td><%= bookmark.description %></td>
        <td><%= bookmark.url %></td>
        <td><%= link_to 'Show', bookmark %></td>
        <td><%= link_to 'Edit', edit_bookmark_path(bookmark) %></td>
        <td><%= link_to 'Destroy', bookmark, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Bookmark', new_bookmark_path %>
```

app/views/bookmarks/_form.html.erb

```html
<%= form_for(@bookmark) do |f| %>
  <% if @bookmark.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@bookmark.errors.count, "error") %> prohibited this bookmark from being saved:</h2>

      <ul>
      <% @bookmark.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :description %><br>
    <%= f.text_area :description %>
  </div>
  <div class="field">
    <%= f.label :url %><br>
    <%= f.text_field :url %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

app/views/bookmarks/index.json.jbuilder

```ruby
json.array!(@bookmarks) do |bookmark|
  json.extract! bookmark, :id, :title, :description, :url
  json.url bookmark_url(bookmark, format: :json)
end
```

## console

```rails console``` で対話的に操作できます。

```sh
% rails console
Loading development environment (Rails 4.2.4)
irb(main):001:0>
```

```Bookmark.last``` で最後に登録したBookmarkを取得します。

```ruby
irb(main):001:0> Bookmark.last
  Bookmark Load (0.2ms)  SELECT  "bookmarks".* FROM "bookmarks"  ORDER BY "bookmarks"."id" DESC LIMIT 1
=> #<Bookmark id: 1, title: "sadah", description: "My portfolio site.", url: "https://sadah.github.io", created_at: "2015-12-02 00:26:31", updated_at: "2015-12-02 00:26:31">
irb(main):002:0>
```

このようにデータを作成することもできます。

```ruby
irb(main):002:0> bookmark = Bookmark.create(title: "test", description: "test bookmark", url: "http://exapmle.com")
   (4.0ms)  begin transaction
  SQL (5.1ms)  INSERT INTO "bookmarks" ("title", "description", "url", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["title", "test"], ["description", "test bookmark"], ["url", "http://exapmle.com"], ["created_at", "2015-12-03 00:04:59.802792"], ["updated_at", "2015-12-03 00:04:59.802792"]]
   (1.0ms)  commit transaction
=> #<Bookmark id: 2, title: "test", description: "test bookmark", url: "http://exapmle.com", created_at: "2015-12-03 00:04:59", updated_at: "2015-12-03 00:04:59">
irb(main):003:0>
```

Active Recordのクエリについてはこちらがわかりやすいです。

* <a href="http://railsguides.jp/active_record_querying.html" target="_blank">Active Record クエリインターフェイス | Rails ガイド</a>

## debug

pryはirb(標準のRubyコンソール)に代わる、パワフルなコンソールです。

* <a href="https://github.com/pry/pry" target="_blank">pry/pry</a>

Gemfileにpryのgemを追加します。

```ruby
group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug'
  gem 'pry'
  gem 'pry-doc'
  gem 'pry-byebug'
  gem 'pry-rails'
  gem 'awesome_print'
end
```

それぞれのgemはこのような機能を持ちます。

|gem||
|--|--|
|pry|pry本体|
|pry-doc|ドキュメントやメソッドを表示する|
|pry-byebug|デバッグをできるようにする|
|pry-rails|Railsのコンソールをpryにする|
|awesome_print|データ構造などをわかりやすく表示する|


Railsコンソールを立ち上げると、pryが起動していることがわかります。

```ruby
% rails c
Loading development environment (Rails 4.2.4)
[1] pry(main)>
```

show-docでドキュメントが表示されます。

```ruby
[1] pry(main)> show-doc String.nil?

From: object.c (C Method):
Owner: Kernel
Visibility: public
Signature: nil?()
Number of lines: 4

Only the object nil responds true to nil?.

   Object.new.nil?   #=> false
   nil.nil?          #=> true
```

show-methodでメソッドが表示されます。

```ruby
[2] pry(main)> show-method String.nil?

From: object.c (C Method):
Owner: Kernel
Visibility: public
Number of lines: 5

static VALUE
rb_false(VALUE obj)
{
    return Qfalse;
}
```

pryでデバッグしていきます。gemを追加したのでrailsを再起動します。

app/controllers/bookmarks_controller.rb に ```binding.pry``` を追加します。

```ruby
def index
  @bookmarks = Bookmark.all
  binding.pry
end
```

http://localhost:3000/bookmarks を表示します。railsを起動したターミナルでデバッグができます。

```ruby
% rails s
=> Booting WEBrick
=> Rails 4.2.4 application starting in development on http://localhost:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
[2015-12-04 07:43:32] INFO  WEBrick 1.3.1
[2015-12-04 07:43:32] INFO  ruby 2.2.3 (2015-08-18) [x86_64-darwin14]
[2015-12-04 07:43:32] INFO  WEBrick::HTTPServer#start: pid=35325 port=3000


Started GET "/bookmarks" for ::1 at 2015-12-04 07:43:42 +0900
  ActiveRecord::SchemaMigration Load (0.5ms)  SELECT "schema_migrations".* FROM "schema_migrations"
Processing by BookmarksController#index as HTML

From: /Users/sada/git/gs/first-app/app/controllers/bookmarks_controller.rb @ line 8 BookmarksController#index:

6: def index
7:   @bookmarks = Bookmark.all
8:   binding.pry
=> 9: end
```

インスタンス変数などを表示できます。

```ruby
[1] pry(#<BookmarksController>)> @bookmarks
  Bookmark Load (1.2ms)  SELECT "bookmarks".* FROM "bookmarks"
[
    [0] #<Bookmark:0x007feb883cbfb0> {
                 :id => 1,
              :title => "sadah",
        :description => "My portfolio site.",
                :url => "https://sadah.github.io",
         :created_at => Wed, 02 Dec 2015 00:26:31 UTC +00:00,
         :updated_at => Wed, 02 Dec 2015 00:26:31 UTC +00:00
    },
    [1] #<Bookmark:0x007feb883cbc90> {
                 :id => 2,
              :title => "test",
        :description => "test bookmark",
                :url => "http://exapmle.com",
         :created_at => Thu, 03 Dec 2015 00:04:59 UTC +00:00,
         :updated_at => Thu, 03 Dec 2015 00:04:59 UTC +00:00
    }
]
```

helpと入力するとpryの使い方が表示されます。

またエラーが発生した場合、エラーが表示された画面でデバッグを行うこともできます。

## 課題

1. link_toを使ってURLをリンクにしてみましょう。
2. タイトルとURLを必須項目にしましょう。
