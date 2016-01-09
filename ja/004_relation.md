# UserとPhotoの関連付け

ユーザーと写真の関連付けを行います。すでに存在するモデルに対して変更を行い、関連付けができるようにしていきます。

## カラム追加

photosテーブルにuser_idというカラムを追加します。

```sh
rails g migration add_column_to_photos user_id
```

DBに変更を反映させます。

```sh
rake db:migrate
```

## UserとPhotoの関連付け

UserとPhotoの関連付けを行います。

app/models/user.rbをエディタで開きます。この行のあとに

```ruby
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable, :omniauthable
```

この行を追加します。

```ruby
has_many :photos
```

app/models/photo.rbをエディタで開きます。この行のあとに

```ruby
mount_uploader :image, ImageUploader
```

この行を追加します。

```ruby
belongs_to :user
```

app/controllers/photos_controller.rbをエディタで開きます。この行を

```ruby
def new
  @photo = Photo.new
end
```

このように変更します。

```ruby
def new
  @photo = current_user.photos.build
end
```

この行を

```ruby
def photo_params
  params.require(:photo).permit(:image, :caption)
end
```

このように変更します。

```ruby
def photo_params
  params.require(:photo).permit(:image, :caption, :user_id)
end
```

Photoはidから検索していました。ユーザーに関連付くデータを取得するようにします。このコードを

```ruby
def set_photo
  @photo = Photo.find(params[:id])
end
```

このように変更します。

```ruby
def set_current_user_photo
  @photo = current_user.photos.find(params[:id])
end
```

この行を

```ruby
before_action :set_photo, only: [:show, :edit, :update, :destroy]
```

このように変更します。

```ruby
before_action :set_current_user_photo, only: [:edit, :update, :destroy]
```

showではユーザーに紐付かないデータを表示しても問題ないため、このコードを

```ruby
def show
end
```

このように変更します。

```ruby
def show
  @photo = Photo.find(params[:id])
end
```

app/views/photos/_form.html.erbをエディタで開きます。この行のあとに

```html
<div class="field">
  <%= f.label :caption %><br>
  <%= f.text_area :caption %>
</div>
```

この行を追加します。

```html
<%= f.hidden_field :user_id %>
```

サーバーを起動し、ログインできるか確認します。すでにサーバーが起動している場合は、再起動してください。

```sh
rails s
```

ログイン後に新しい写真を登録し、関連付けられているか確認します。サーバーを起動したターミナルとは別のターミナルで、Railsコンソールを起動します。

```sh
rails c
```

最後の写真のユーザーを確認します。

```
Photo.last.user
```

このようにユーザーの情報が表示されていれば、UserとPhotoの関連付けができています。

```ruby
[1] pry(main)> Photo.last.user
  Photo Load (0.2ms)  SELECT  "photos".* FROM "photos"  ORDER BY "photos"."id" DESC LIMIT 1
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT 1  [["id", 1]]
#<User:0x007f8821a5e460> {
                        :id => 1,
                     :email => "0000000-twitter@example.com",
        :encrypted_password => "xxxx",
      :reset_password_token => nil,
    :reset_password_sent_at => nil,
       :remember_created_at => nil,
             :sign_in_count => 5,
        :current_sign_in_at => Tue, 05 Jan 2016 22:34:41 UTC +00:00,
           :last_sign_in_at => Tue, 05 Jan 2016 00:28:43 UTC +00:00,
        :current_sign_in_ip => "::1",
           :last_sign_in_ip => "::1",
                :created_at => Sat, 19 Dec 2015 04:38:22 UTC +00:00,
                :updated_at => Tue, 05 Jan 2016 22:34:41 UTC +00:00,
                  :provider => "twitter",
                       :uid => "0000000",
                 :user_name => "xxxxx",
                :avatar_url => "http://pbs.twimg.com/profile_images/xxx.jpg"
}
```

この状態では、ログインせずに写真を登録しようとするとエラーが発生します。次はログインチェックを実装します。

これまでに作成したデータは、UserとPhotoが関連付けられていないため削除します。Railsコンソールで以下のコードを実行すると、すべてのPhotoが削除できます。

```
Photo.all.each(&:destroy)
```
