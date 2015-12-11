# 画像アップロード(CarrierWave)

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

サーバーを再起動します。サーバーすでに起動している場合は Ctrl + c で停止してください。

サーバーを起動してください。ターミナルで以下のコマンドを実行します。

```sh
rails s
```

サーバーが起動したら、写真のアップロードを試してみてください。
