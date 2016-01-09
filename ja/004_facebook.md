# 認証機能(Devise/Facebook)

Facebookログイン機能を実装します。

## Facebook Developer登録

Facebookログインを行うため、アプリケーション登録を済ませておいてください。

Facebookにログインした状態で [https://developers.facebook.com](https://developers.facebook.com) にアクセスします。

My Appsから「Add a New App」ボタンでアプリケーション登録を行います。プラットフォームにはウェブサイトを選択します。

App ID、カテゴリを設定し、アプリケーションを登録します。Site URLには http://localhost:3000 を設定します。

画面をリロードし、My Appsから作成したアプリケーションを選択すると、ダッシュボードが表示されます。ここにApp IDとApp Secretがあり、これらを利用してFacebookログインを行います。

## OmniAuth Facebookの導入

OmniAuth Facebookのgemをインストールします。

* <a href="https://github.com/mkdynamic/omniauth-facebook" target="_blank">mkdynamic/omniauth-facebook</a>

Gemfileをエディタで開き、この行の後に

```
gem 'omniauth-twitter'
```

この行を追加します。

```
gem 'omniauth-facebook'
```

gemをインストールします。RAILS_ROOTでbundle installを実行します。

```
bundle install
```

## Facebook認証の設定

[https://developers.facebook.com](https://developers.facebook.com) で、ダッシュボードから作成したアプリケーションの情報を表示します。

* App ID
* App Secret

config/secrets.ymlにFacebook認証で利用するKeyを追加します。Twitterの設定の後にFacebookの設定を追加します。これらの行のあとに

```ruby
default_twitter: &default_twitter
  twitter_api_key: (API Keyの値)
  twitter_api_secret: (API Secretの値)
```

これらのコードを追加します。

```ruby
default_facebook: &default_facebook
  facebook_api_key: (App IDの値)
  facebook_api_secret: (App Secretの値)
```

development: と test: のTwitter設定の後にFacebook認証の情報を追加します。

```ruby
development:
  secret_key_base: 9cbxxxxx617b21163a7d31f1280e6973a62ea0a21a98e233173dd29ccde7809ea3eef72d9e220216b3e2fea1a82b7013c632a89f0acf4b8f77713e7d9528fc8b
  <<: *default_twitter
  <<: *default_facebook

test:
  secret_key_base: 4fbxxxxx4de9c47525a3365728fca18fa5e4401aeff049b8c7a3a624e828ebc2aa94d53693a5a01f348d1abd33e363e4fbb3da84742702be431c39e569b1881b
  <<: *default_twitter
  <<: *default_facebook
```

本番環境では環境変数からFacebook認証の情報を取得するようにします。この行のあとに

```ruby
twitter_api_secret: <%= ENV["FACEBOOK_API_SECRET"] %>
```

この行を追加します。

```ruby
facebook_api_key: <%= ENV["FACEBOOK_API_KEY"] %>
facebook_api_secret: <%= ENV["FACEBOOK_API_SECRET"] %>
```

config/initializers/devise.rbで、Facebook認証で利用するKeyを設定します。この行のあとに

```ruby
  Rails.application.secrets.twitter_api_secret
```

これらのコードを追加します。

```ruby
  config.omniauth :facebook,
    Rails.application.secrets.facebook_api_key,
    Rails.application.secrets.facebook_api_secret
```

## Facebook認証処理の実装

app/controllers/users/omniauth_callbacks_controller.rb をエディタで開きます。これらの行のあとに

```
def twitter
  callback_from :twitter
end
```

これらのコードを追加します。

```
def facebook
  callback_from :facebook
end
```

app/models/user.rbをエディタで開きます。self.find_or_create_from_oauthメソッドを以下のように書き換えます。

```ruby
def self.find_or_create_from_oauth(auth)
  User.find_or_create_by(provider: auth.provider, uid: auth.uid) do |user|
    user.user_name  = auth.info.nickname || auth.info.name
    user.avatar_url = auth.info.image
    user.email      = User.dummy_email(auth)
    user.password   = Devise.friendly_token[0, 20]
  end
end
```

ログインを行うためのリンクを追加します。

app/views/layouts/application.html.erbをエディタで開きます。この行のあとに

```ruby
<%= link_to 'Twitter login', user_omniauth_authorize_path(:twitter) %>
```

この行を追加します。

```ruby
<%= link_to 'facebook login', user_omniauth_authorize_path(:facebook) %>
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
