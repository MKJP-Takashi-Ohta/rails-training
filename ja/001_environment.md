#  Ruby on Rails 環境構築

この手順は OS X 10.8, 10.9, 10.10 を対象としています。

## Xcode インストール

App Store から Xcode をインストールする。一度 Xcode を起動し、ライセンス使用許諾契約に同意する。

## Command line tools インストール

Command line tools をインストールする。

```
xcode-select --install
```

## Homebrew インストール

<a href="http://brew.sh/index_ja.html" target="_blank">Homebrew</a> をインストールする。

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## rbenv インストール

<a href="https://github.com/sstephenson/rbenv" target="_blank">rbenv</a> をインストールする。

```
brew update
brew install rbenv rbenv-gem-rehash ruby-build
```

bash を利用している場合、以下のコマンドで環境変数を設定する。zshなどの場合、適宜書き換えて実行する。

```
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
echo 'export PATH="$HOME/.rbenv/shims:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```

## Ruby インストール

Rubyをインストールする

```
rbenv install 2.2.3
```

デフォルトのRubyバージョンを指定する。

```
rbenv global 2.2.3
```

## Ruby on Rails インストール

Ruby on Rails をインストールする。

```
gem install rails --no-ri --no-rdoc
```

## 確認

以下のコマンドが正しく実行できれば、Railsのインストールは正しくできています。

```
rails new sample-project
```

## その他

Windows環境はサポート対象外ですが、<a href="http://railsinstaller.org/en" target="_blank">RailsInstaller</a> が便利だと思います。

* https://s3.amazonaws.com/railsinstaller/Windows/railsinstaller-3.1.0.exe
