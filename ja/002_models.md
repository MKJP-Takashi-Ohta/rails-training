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

## 画像アップロード

CarrierWaveというgemを使うと、簡単に画像のアップロードが実装できます。

* <a href="https://github.com/carrierwaveuploader/carrierwave" target="_blank">carrierwaveuploader/carrierwave</a>

エディタでGemfileを編集します。この行の後に

```ruby
# bundle exec rake doc:rails generates the API under doc/api.
gem 'sdoc', '~> 0.4.0', group: :doc
```

この1行を追加します。

```ruby
gem 'carrierwave'
```

```bundle install``` を実行してgemをインストールします。ターミナルで以下のコマンドを実行します。

```sh
bundle install
```

画像をアップロードするためのコードを生成します。ターミナルで以下のコマンドを実行します。

```sh
rails generate uploader Image
```

エディタでapp/models/photo.rbを編集します。この行の後に

```ruby
class Photo < ActiveRecord::Base
```

この行を追加します。

```ruby
  mount_uploader :image, ImageUploader
```

画像ファイルをアップロードできるようにします。エディタでapp/views/photos/_form.html.erbを編集します。

この行を

```ruby
<%= f.text_field :image %>
```

このように変更します。

```ruby
<%= f.file_field :image %>
```

画像を表示できるようにします。エディタでapp/views/photos/show.html.erbを編集します。

この行を

```ruby
<%= @photo.image %>
```

このように変更します。

```ruby
<%= image_tag(@photo.image_url) if @photo.image.present? %>
```

## Bootstrapの導入

エディタで app/views/layouts/application.html.erb を編集します。この行の後に

```
<title>Photolog</title>
```

この2行を追加します。

```
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
```

この行の前後を

```
<%= yield %>
```

このように変更します。

```
<div class="container">
  <%= yield %>
</div>
```

この行の前に

```
</body>
```

この行を追加します

```
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
```

### 課題

画像のサイズを指定しましょう
他の画面でも画像を表示出来るようにしてみましょう。
Bootstrapを組み込んでみましょう。
resize_to_fit
