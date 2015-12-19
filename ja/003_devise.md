# 認証機能(Devise)

ログインやユーザ管理の機能を持つDeviseと、Twitter認証の機能を持つOmniAuth Twitterを利用して、Twitter認証の機能を実装します。

実装の流れは以下のようになります。

1.DeviseとOmniAuth Twitterの導入
2.Userモデルの作成
3.認証処理の実装
4.Twitter認証の設定

## Deviseの導入

DeviseとOmniAuth Twitterのgemをインストールします。

* <a href="https://github.com/plataformatec/devise" target="_blank">plataformatec/devise</a>
* <a href="https://github.com/arunagw/omniauth-twitter" target="_blank">arunagw/omniauth-twitter</a>

Gemfileをエディタで開き、この行の後に

```
gem 'carrierwave'
```

この行を追加します。

```
gem 'devise'
gem 'omniauth-twitter'
```

gemをインストールします。RAILS_ROOTでbundle installを実行します。

```
bundle install
```

Deviseのファイルを生成します。

```
rails g devise:install
```

実行するとこのようなメッセージが表示されます。

```
% rails g devise:install
      create  config/initializers/devise.rb
      create  config/locales/devise.en.yml
===============================================================================

Some setup you must do manually if you haven't yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. If you are deploying on Heroku with Rails 3.2 only, you may want to set:

       config.assets.initialize_on_precompile = false

     On config/application.rb forcing your application to not access the DB
     or load models when precompiling your assets.

  5. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

===============================================================================
```

メッセージにしたがって、必要な部分の対応を行います。

今回は利用しませんが、Deviseの機能を使うとユーザ作成や認証時にメールを送信できます。この機能を利用するための設定を追加します。

config/environments/development.rbをエディタで開きます。この行の後に

```ruby
# Don't care if the mailer can't send.
config.action_mailer.raise_delivery_errors = false
```

この行を追加します。

```ruby
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

root_url(開発環境の場合は http://localhost:3000 )にアクセスした時に表示されるページを指定します。今回は /photos にリダイレクトさせます。

config/routes.rbをエディタで開きます。この行のあとに

```ruby
Rails.application.routes.draw do
```

この行を追加します。

```ruby
  root to: redirect('/photos')
```

認証した時のメッセージを表示できるようにします。

app/views/layouts/application.html.erbをエディタで開きます。この行のあとに

```html
<div class="container">
```

この行を追加します。

```html
  <p class="notice"><%= notice %></p>
  <p class="alert"><%= alert %></p>
```

メッセージが重複して表示されてしまうため、不要になったコードを削除します。

app/views/photos/index.html.erb をエディタで開きます。この行を削除します。

```html
<p id="notice"><%= notice %></p>
```

4と５の対応はいまは必要ないのでなにもしません。

## Userモデルの作成

認証で利用するUserモデルを自動生成します。

```sh
rails g devise User
```

コードが自動生成されます。

```sh
rails g devise User
      invoke  active_record
      create    db/migrate/20151216140212_devise_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml
      insert    app/models/user.rb
       route  devise_for :users
```

マイグレーションファイルはこのような内容になっています。Deviseにはアカウントロックなどの機能があり、それらに必要なカラムが定義されています。

db/migrate/20151216140212_devise_create_users.rb

```ruby
class DeviseCreateUsers < ActiveRecord::Migration
  def change
    create_table(:users) do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      t.integer  :sign_in_count, default: 0, null: false
      t.datetime :current_sign_in_at
      t.datetime :last_sign_in_at
      t.string   :current_sign_in_ip
      t.string   :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
end
```

Userモデルはこのようなコードになっています。Deviseで利用できるモジュールが定義されています。

```ruby
class User < ActiveRecord::Base
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
end
```

Twitter認証で利用するカラムを追加します。

|カラム|説明|
|:--|:--|
|provider|認証で使うサービス。OmniAuthTwitterで利用します|
|uid|ユーザーのID。OmniAuthTwitterで利用します|
|user_name|ユーザ名|
|avatar_url|アイコン画像のURL|

マイグレーションファイルを自動生成します。

```sh
rails g migration add_columns_to_users provider uid user_name avatar_url
```

マイグレーションファイルが自動生成されました。

```sh
rails g migration add_columns_to_users provider uid user_name avatar_url
      invoke  active_record
      create    db/migrate/20151216235406_add_columns_to_users.rb
```

マイグレーションファイルはこのような内容になっています。

db/migrate/20151216235406_add_columns_to_users.rb

```ruby
class AddColumnsToUsers < ActiveRecord::Migration
  def change
    add_column :users, :provider, :string
    add_column :users, :uid, :string
    add_column :users, :user_name, :string
    add_column :users, :avatar_url, :string
  end
end
```

マイグレーションを実行します。

```sh
rake db:migrate
```

このような実行結果が表示されます。

```sh
rake db:migrate
== 20151217223032 AddColumnsToUsers: migrating ================================
-- add_column(:users, :provider, :string)
   -> 0.0007s
-- add_column(:users, :uid, :string)
   -> 0.0003s
-- add_column(:users, :user_name, :string)
   -> 0.0004s
-- add_column(:users, :avatar_url, :string)
   -> 0.0003s
== 20151217223032 AddColumnsToUsers: migrated (0.0019s) =======================
```

## Twitter認証の設定

[https://apps.twitter.com/](https://apps.twitter.com/) で、作成したアプリケーションの情報を表示します。「Keys and Access Tokens」のタブをクリックすると以下の情報が表示されます。

* Consumer Key (API Key)
+ Consumer Secret (API Secret)

config/secrets.ymlにTwitter認証で利用するKeyを追加します。development: の前に設定を追加します。

```ruby
default_twitter: &default_twitter
  twitter_api_key: (API Keyの値)
  twitter_api_secret: (API Secretの値)
```

development: と test: の設定のあとにTwitter認証の情報を追加します。

```ruby
development:
  secret_key_base: 9cbxxxxx617b21163a7d31f1280e6973a62ea0a21a98e233173dd29ccde7809ea3eef72d9e220216b3e2fea1a82b7013c632a89f0acf4b8f77713e7d9528fc8b
  <<: *default_twitter

test:
  secret_key_base: 4fbxxxxx4de9c47525a3365728fca18fa5e4401aeff049b8c7a3a624e828ebc2aa94d53693a5a01f348d1abd33e363e4fbb3da84742702be431c39e569b1881b
  <<: *default_twitter
```

本番環境では環境変数からtwitter認証の情報を取得するようにします。この行のあとに

```ruby
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

この行を追加します。

```ruby
  twitter_api_key: <%= ENV["TWITTER_API_KEY"] %>
  twitter_api_secret: <%= ENV["TWITTER_API_SECRET"] %>
```

config/initializers/devise.rbで、Twitter認証で利用するKeyを設定します。

最後のendの前に3行追加します。

```ruby
  config.omniauth :twitter,
    Rails.application.secrets.twitter_api_key,
    Rails.application.secrets.twitter_api_secret
```

app/models/user.rb に OmniAuth を利用する設定を追加します。この行の最後に

```ruby
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable
```

:omniauthable を追加します。

```ruby
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable, :omniauthable
```

## 認証処理の実装

Twitter認証は以下のような流れで行います。

1. ログインリンクをクリックする
2. Twitterの認証画面にリダイレクトする
3. Twitterと連携する
4. Twitterからのコールバックでアプリケーションにリダイレクトされる
5. Twitterからアクセストークンを使ってユーザーの認証 or ユーザーの登録を行う

2-4はDeviseの設定を行うことで実現できるため、実装する必要はありません。1と5の実装を行います。

### コールバック用コントローラーの実装

最初にTwitterからのコールバックを受け取るコントローラーを自動生成します。

```sh
rails g controller users/omniauth_callbacks
```

このようなファイルが自動生成されます。

```sh
rails g controller users/omniauth_callbacks
      create  app/controllers/users/omniauth_callbacks_controller.rb
      invoke  erb
      create    app/views/users/omniauth_callbacks
      invoke  test_unit
      create    test/controllers/users/omniauth_callbacks_controller_test.rb
      invoke  helper
      create    app/helpers/users/omniauth_callbacks_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/users/omniauth_callbacks.coffee
      invoke    scss
      create      app/assets/stylesheets/users/omniauth_callbacks.scss
```

コントローラーにコールバックを受け取るアクションを実装します。

app/controllers/users/omniauth_callbacks_controller.rb をエディタで開きます。編集前はこのような内容になっています。

```ruby
class Users::OmniauthCallbacksController < ApplicationController
end
```

このように書き換えます。

```ruby
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController

  def twitter
    callback_from :twitter
  end

  private

  def callback_from(provider)
    provider = provider.to_s

    @user = User.find_or_create_from_oauth(request.env['omniauth.auth'])

    if @user.persisted?
      flash[:notice] = I18n.t('devise.omniauth_callbacks.success', kind: provider.capitalize)
      session[:user_id] = @user.id
      sign_in_and_redirect @user, event: :authentication
    else
      session["devise.#{provider}_data"] = request.env['omniauth.auth']
      redirect_to new_user_registration_url
    end
  end

end
```

ApplicationControllerではなくDevise::OmniauthCallbacksControllerを継承するようにします。

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
```

プロバイダー名(サービス)に対応したアクションが必要なため、twitterというアクションを実装しています。

```ruby
def twitter
  callback_from :twitter
end
```

実際の処理はcallback_fromメソッドが担当します。

ユーザーがすでに存在する場合はDBから取得し、存在しない場合は新規作成します。このメソッドはあとでmodelに実装します。request.env['omniauth.auth']にはTwitterからのアクセストークンが格納されています。

```ruby
@user = User.find_or_create_from_oauth(request.env['omniauth.auth'])
```

@user.persisted?でモデルが保存済みか確認します。

```ruby
if @user.persisted?
```

正しく保存されている場合は、表示するメッセージを設定し、Deviseの機能を使ってリダイレクトします。

### ユーザー情報の検索と保存

Userモデルでユーザー情報の検索と保存をできるようにします。

```ruby
class User < ActiveRecord::Base
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :omniauthable

  def self.find_or_create_from_oauth(auth)
    User.find_or_create_by(provider: auth.provider, uid: auth.uid) do |user|
      user.user_name   = auth.info.nickname
      user.avatar_url = auth.info.image
      user.email      = User.dummy_email(auth)
      user.password   = Devise.friendly_token[0, 20]
    end
  end

  private

  def self.dummy_email(auth)
    "#{auth.uid}-#{auth.provider}@example.com"
  end

end
```

ユーザーがすでに存在する場合はDBから取得し、存在しない場合は新規作成します。
find_or_create_byメソッドはActiveRecordの機能です。ユーザーが存在しなかった場合は、Twitterからのアクセストークンの情報を使ってユーザーを新規作成しています。

```ruby
def self.find_or_create_from_oauth(auth)
  User.find_or_create_by(provider: auth.provider, uid: auth.uid) do |user|
    user.user_name   = auth.info.nickname
    user.avatar_url = auth.info.image
    user.email      = User.dummy_email(auth)
    user.password   = Devise.friendly_token[0, 20]
  end
end
```

emailは今回利用ないため、ダミーデータを登録しています。ダミーデータを作成するメソッドを実装します。

```ruby
def self.dummy_email(auth)
  "#{auth.uid}-#{auth.provider}@example.com"
end
```

Deviseが利用するURLを定義します。

config/routes.rbをエディターで開ます。この行を

```ruby
devise_for :users
```

このように書き換えます。

```ruby
devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
```

ログイン、ログアウトを行うためのリンクを追加します。

app/views/layouts/application.html.erbをエディタで開きます。この行のあとに

```html
<div class="container">
```

これらのコードを追加します。

```ruby
<% if user_signed_in? %>
  <%= link_to 'logout', destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to 'Twitter login', user_omniauth_authorize_path(:twitter) %>
<% end %>
```

user_signed_in?はDeviseの機能で、ログインしているかを確認できます。

```ruby
<% if user_signed_in? %>
```

サーバーを起動し、ログインできるか確認します。

```sh
rails s
```

ログイン後にユーザーが作成されているか確認します。サーバーを起動したターミナルとは別のターミナルで、Railsコンソールを起動します。

```sh
rails c
```

コンソールで最後のユーザを取得します。

```
User.last
```

このような情報が保存されていました。

```ruby
[1] pry(main)> User.last
  User Load (0.5ms)  SELECT  "users".* FROM "users"  ORDER BY "users"."id" DESC LIMIT 1
#<User:0x007fe6020122b0> {
                        :id => 1,
                     :email => "1111111-twitter@example.com",
        :encrypted_password => "xxxxx",
      :reset_password_token => nil,
    :reset_password_sent_at => nil,
       :remember_created_at => nil,
             :sign_in_count => 2,
        :current_sign_in_at => Thu, 17 Dec 2015 22:53:46 UTC +00:00,
           :last_sign_in_at => Thu, 17 Dec 2015 22:53:09 UTC +00:00,
        :current_sign_in_ip => "::1",
           :last_sign_in_ip => "::1",
                :created_at => Thu, 17 Dec 2015 22:53:09 UTC +00:00,
                :updated_at => Thu, 17 Dec 2015 22:53:46 UTC +00:00,
                  :provider => "twitter",
                       :uid => "1111111",
                 :user_name => "sada_h",
                :avatar_url => "http://pbs.twimg.com/profile_images/660349716178292737/y16rKnHm_normal.jpg"
```

### 課題

* ログイン後にユーザー名とユーザーのアイコンが表示出来るようにしましょう。
* Facebook認証を実装しましょう
* photoとuserを紐付けましょう
* ログインしていないとphotoの新規登録/編集/削除ができないようにしましょう。
