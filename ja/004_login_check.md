# ログインチェック

各ページでログインのチェックを行い、ログインしている場合のみ画像のアップロードをできるようにします。
また他人のアップロードした画像の編集、削除をできないようにします。

ログインのチェックは、ViewとControllerの両方で行います。

## Controllerのログインチェック

app/controllers/photos_controller.rb をエディタで開きます。

ログインチェックを行うメソッドを実装します。ログイン指定なかった場合は、メッセージを表示しindexにリダイレクトするようにします。この行のあとに

```ruby
private
```

これらのコードを追加します。

```ruby
def login_check
  unless user_signed_in?
    flash[:alert] = "ログインしてください"
    redirect_to root_path
  end
end
```

この行のあとに

```ruby
class PhotosController < ApplicationController
```

この行を追加します。login_checkメソッドを :new, :edit, :update, :destroy のアクションの前に実行し、ログインチェックを行います。

```ruby
before_action :login_check, only: [:new, :edit, :update, :destroy]
```

サーバーを起動します。すでにサーバーが起動している場合は、再起動してください。

```sh
rails s
```

ログインしていない状態で画像の編集や追加をしようとすると、メッセージが表示されindexにリダイレクトされることを確認してください。

## Viewのログインチェック

Viewを修正します。

app/views/photos/index.html.erb をエディタで開きます。この行の前後を変更します。

```html
<p><%= link_to 'New Photo', new_photo_path %></p>
```

このように変更します。

```html
<% if user_signed_in? %>
  <p><%= link_to 'New Photo', new_photo_path %></p>
<% end %>
```

app/views/photos/show.html.erb をエディタで開きます。この行の前後を変更します。

```html
<p>
  <%= link_to 'Edit', edit_photo_path(@photo) %>
  <%= link_to 'Destroy', @photo, method: :delete, data: { confirm: 'Are you sure?' } %>
</p>
```

このように変更します。ログイン済みで、写真のユーザーとログインユーザーが一致している場合は、リンクを表示します。

```html
<% if user_signed_in? && @photo.user == current_user %>
  <p>
    <%= link_to 'Edit', edit_photo_path(@photo) %>
    <%= link_to 'Destroy', @photo, method: :delete, data: { confirm: 'Are you sure?' } %>
  </p>
<% end %>
```

```@photo.user``` で、PhotoからUserを取得しています。このままだとViewからDBに対するクエリーが発行されてしまうため、Controllerで合わせて取得するようにします。

app/controllers/photos_controller.rb をエディタで開きます。このコードを

```ruby
def show
  @photo = Photo.find(params[:id])
end
```

このように変更します。

```ruby
def show
  @photo = Photo.includes(:user).find(params[:id])
end
```
