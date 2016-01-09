# ユーザー情報の表示

ログイン後にユーザー名とユーザーのアイコンが表示出来るようにします。

app/views/layouts/application.html.erbをエディタで開きます。この行のあとに

```html
<% if user_signed_in? %>
```

これらのコードを追加します。

```ruby
    <%= image_tag current_user.avatar_url %>
    <%= current_user.user_name %>
```
