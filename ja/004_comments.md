# コメント機能

アプリケーションにコメント機能を追加します。ログインしているユーザーがコメントできるようにします。

## scaffold

コメントのscaffoldを自動生成します。コメントはUserとPhotoに関連付けます。コンソールで以下のコマンドを実行します。

```sh
rails g scaffold comment user_id:integer photo_id:integer body:text
```

マイグレーションを行います。コンソールで以下のコマンドを実行します。

```sh
rake db:migrate
```

## CommentとUserとPhotoの関連付け

app/models/user.rb をエディタで開きます。この行のあとに

```ruby
has_many :photos
```

この行を追加します。

```ruby
has_many :comments
```

app/models/photo.rb をエディタで開きます。この行のあとに

```ruby
belongs_to :user
```

この行を追加します。

```ruby
has_many :comments
```

app/models/comment.rb をエディタで開きます。この行のあとに

```ruby
class Comment < ActiveRecord::Base
```

この行を追加します。

```ruby
  belongs_to :user
  belongs_to :photo
```

サーバーを起動したターミナルとは別のターミナルで、Railsコンソールを起動します。

```sh
rails c
```

PhotoとCommentが関連付けられているか確認します。まだデータがないので何も取得できませんが、エラーなどが発生しないことを確認します。

```
Photo.last.comments
```

UserとCommentが関連付けられているか確認します。まだデータがないので何も取得できませんが、エラーなどが発生しないことを確認します。

```
User.last.comments
```

## Controllerの修正

app/controllers/photos_controller.rb をエディタで開きます。showアクションを修正して、コメントを表示できるようにします。このコードを

```ruby
def show
  @photo = Photo.includes(:user).find(params[:id])
end
```

このように変更してください。

```ruby
def show
  @photo = Photo.includes(:user).find(params[:id])
  @comments = @photo.comments.includes(:user).all
  @comment  = @photo.comments.build(user_id: current_user.id) if current_user
end
```

```@comments``` にPhotoに関連したコメントを格納します。user_nameの表示を行うため、コメントに関連したUserも取得しておきます。

```ruby
@comments = @photo.comments.includes(:user).all
```

```@comment``` はコメントを新規作成するためのインスタンスです。

```ruby
@comment  = @photo.comments.build(user_id: current_user.id) if current_user
```

Photoに関連させるため ```@photo.comments.build``` としてインスタンスを生成します。
buildの引数にuser_idを渡して、Userとも関連付けします。
ログインしていない場合はコメントできないため、@commentは不要です。

## Viewの修正

app/views/photos/show.html.erb をエディタで開きます。このコードのあとに

```
  <% end %>
</div>
```

このコードを追加します。

```html
<div>
  <% @comments.each do |comment| %>
    <div>
      <strong><%= comment.user.user_name %></strong>
      <br />
      <p><%= comment.body %></p>
      <% if user_signed_in? && comment.user == current_user %>
        <p><%= link_to 'Delete', comment_path(comment), method: :delete %></p>
      <% end %>
    </div>
  <% end %>
  <% if user_signed_in? %>
    <%= render 'comments/form' %>
  <% end %>
</div>
```

コメントしたユーザーのuser_nameは、commentの関連から取得します。

```html
<strong><%= comment.user.user_name %></strong>
```

コメントしたユーザーのみ、コメントの削除ができるようにします。

```html
<% if user_signed_in? && comment.user == current_user %>
  <p><%= link_to 'Delete', comment_path(comment), method: :delete %></p>
<% end %>
```

ログインしていないと、コメントできないようにします。

```html
<% if user_signed_in? %>
  <%= render 'comments/form' %>
<% end %>
```

app/views/comments/_form.html.erb をエディタで開きます。これらのコードを削除し

```html
<div class="field">
  <%= f.label :user_id %><br>
  <%= f.number_field :user_id %>
</div>
<div class="field">
  <%= f.label :photo_id %><br>
  <%= f.number_field :photo_id %>
</div>
```

これらのコードを追加します。

```html
<%= f.hidden_field :user_id %>
<%= f.hidden_field :photo_id %>
```

user_idやphoto_idは表示する必要がないので、hiddenフィールドとして保持します。

サーバーを起動し、コメントできるか確認します。すでにサーバーが起動している場合は、再起動してください。

```sh
rails s
```
