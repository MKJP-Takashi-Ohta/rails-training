# デザインの適用(Bootstrapの導入)

Bootstrapを導入して、デザインを適用していきます。

* <a href="http://getbootstrap.com/" target="_blank">Bootstrap · The world's most popular mobile-first and responsive front-end framework.</a>

## レイアウトファイルの編集

エディタで app/views/layouts/application.html.erb を編集します。この行の後に

```html
<title>Photolog</title>
```

この2行を追加します。

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
```

この行の前後を

```ruby
<%= yield %>
```

このように変更します。

```html
<div class="container">
  <%= yield %>
</div>
```

この行の前に

```html
</body>
```

この行を追加します

```html
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
```

## show.html.erb の編集

エディタで ```app/views/photos/show.html.erb``` を編集します。

この行以降の内容を

```html
<p id="notice"><%= notice %></p>
```

この内容に変更します。

```html
<div class="row">
  <div class="col-lg-12">
    <%= image_tag(@photo.image_url) if @photo.image.present? %>
    <div>
      <h4><%= @photo.caption %></h4>
      <p>
        <%= link_to 'Edit', edit_photo_path(@photo) %>
        <%= link_to 'Destroy', @photo, method: :delete, data: { confirm: 'Are you sure?' } %>
      </p>
    </div>
  </div>
</div>
```

## 課題

* ```app/views/photos/index.html.erb``` でも画像ファイルを表示しましょう
* ```app/views/photos/index.html.erb``` でも表示を整えてみましょう。
* 画像のサイズを指定しましょう

さらに頑張りたい人はレスポンシブウェブデザインに挑戦しましょう。

* Media Queriesを使って、レスポンシブウェブデザインを適用しましょう
* 画面が一定サイズより小さい場合は、画像が横幅いっぱいに表示されるようにしましょう

画面が大きい場合はこのように表示され

<img src="../images/photo-l.jpg" alt="画面が大きい場合" style="border: 1px solid #eee">

画面が小さい場合はこのように表示されるようにしてみましょう。

<img src="../images/photo-s.jpg" alt="画面が小さい場合" style="border: 1px solid #eee">
