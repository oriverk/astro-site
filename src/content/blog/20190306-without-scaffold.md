---
create: '2019-03-06'
update: '2019-12-04'
title: 'Day 2 ~ 8：Scaffold なしでの掲示板作成'
tags: [cebu, rails]
published: true
---

from Qiita:

- [2日目(2)：Scaffoldを用いた掲示板作成](https://qiita.com/OriverK/items/3b12f4d2f09f207a4dfa)
- [3日目：ScaffoldなしでのApp作成](https://qiita.com/OriverK/items/c1cbcc7e1a92bfced79f)
- [4日目：Scaffold無しでフォーム作成：ModelとView](https://qiita.com/OriverK/items/c3cc42eee20219361a1c)
- [8日目(1)：Scaffoldなしの掲示板作成：まとめ](https://qiita.com/OriverK/items/589efb97d7c6667319b9)

## 使用環境

- 仮想環境 OS: Ubuntu 18.04
- Ruby：2.51
  - Rails:5.2.2

### rails db:migrate

- [Railsドキュメントより](http://railsdoc.com/references/rake%20db:migrate)
  - rails db:migrate を実行
  - schema_migrations テーブルを調べ、存在しなければ作成
  - db/migrate ディレクトリ内のすべてのマイグレーションファイルを調べる
  - データベースの現在のバージョンと異なるバージョンがあった場合、データベースに適応
  - schema_migrations テーブルの更新

---

## 3日目

`scaffold` を利用せずに App 作成をし、Scaffold の有難みを知る。

### 前準備

1. rails s new qiita_routes -d MySQL
2. Gemfile の miniracer コメントインして、bundle install
3. Config/database.yml の password 情報編集
4. rails db:create

### 前提：知識

#### ページ作成に必要なもの

- view
  - 今日はしなかったので、今投稿には未記載
  - view の中身がブラウザに表示される内容
- controller
  - ページ表示の際、controller を経由して、view をブラウザに返す
  - controller で設定した action は、controller と同じ名前の view フォルダの中から、action と同じ名前の html ファイルを探してブラウザに返す
- routing
  - ブラウザと controller を繋ぐ

#### ページ表示の流れ

**Routing => Controller => Model => View**
model はデータベース情報が必要なときだけ使用。

### 本段階

#### controllerを作成

```sh
#rails generate controller コントローラ名 (+アクション名)
rails generate controller Users
```

#### routingの設定：ブラウザとコントローラをつなぐ

```rb title=config/routes.rb
Rails.application.routes.draw do
  get 'users', action: :index, controller: 'users'
end

```

```sh
rails routes

# Prefix Verb URI Pattern
# Controller#Action
# users GET  /users(.:format) 　 #追加された行
# users#index   　　　　　　　　　#追加された行
```

#### controller：modelとviewをつなぐ

```rb title=app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
    render plain: 'Hello'
  end
end
```

無事に、ブラウザ上で Hello が表示された。

#### renderメソッド

上 controller 編集時に用いた、render メソッドは実際に画面に表示される内容を生成する。今回の render の plain オプションを指定すると、文字列を直接表示できる。
Rails の controller で render を省略すると、代わりに app/views/コントローラ名/アクション名.html.erb を用いる。
=>　controller 作成コマンドは `rails g controller コントローラ名　アクション名`

参照: [Ruby on Rails でページを作成する仕組み by @np_misaki氏](https://qiita.com/np_misaki/items/1c5ff951272a91f70e5f)

- Config : アプリケーションの設定情報を格納する
- /routes.rb : ルーティングを設定する
- /locales : 辞書ファイル(グローバル対応等)
- /app : アプリケーション開発中にメインで使用するディレクトリ
- /controllers..Controller クラスを格納する
- /models :　Model クラスを格納する
- /views :View クラスを格納する

---

## 4日目

## モデルを作成

- model とは
  - データベースを操作する
  - app/models 下に配置される
  - テーブルごとに用意され、データの登録・取得・更新・削除などを行なう

### model作成コマンド

```sh
# rails generate model モデル名 カラム名:データ型 カラム名:データ型 ...
rails generate model User name:string email:string sex:integer age:integer address:integer attendance:integer opinion:text
# string型は文字型、integer型は整数型
```

## DBの操作

### テーブルの作成をする

```sh
rails db:migrate
```

### できたテーブルをMySQL側で確認してみる

```sql
-- mysql
USE scanashi0307_development;
-- Reading table information for completion of table and column names
-- You can turn off this feature to get a quicker startup with -A

-- Database changed
-- mysql> SHOW TABLES;
-- +------------------------------------+
-- | Tables_in_scanashi0307_development |
-- +------------------------------------+
-- | ar_internal_metadata               |
-- | schema_migrations                  |
-- | users                              |
-- +------------------------------------+
-- 3 rows in set (0.00 sec)

-- usersテーブルが作成されたことが分かる。
```

```sql
SHOW CREATE TABLE users;

-- | users | CREATE TABLE `users` (
--   `id` bigint(20) NOT NULL AUTO_INCREMENT,
--   `name` varchar(255) DEFAULT NULL,
--   `email` varchar(255) DEFAULT NULL,
--   `sex` int(11) DEFAULT NULL,
--   `age` int(11) DEFAULT NULL,
--   `address` int(11) DEFAULT NULL,
--   `attendance` int(11) DEFAULT NULL,
--   `opinion` text,
--   `created_at` datetime NOT NULL,
--   `updated_at` datetime NOT NULL,
--   PRIMARY KEY (`id`)
-- ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
```

`rails g models` で設定したカラム名が作成されているのがわかる。

### データベースにfooさんのレコードを追加してみる

```sql
INSERT INTO `users` (`name`, `email`, `sex`, `age`, `address`, `attendance`, `opinion`, `created_at`, `updated_at`) VALUES ('foo', 'foo@gmail.com', 1, 23, 2, 0, 'foooo', '2017-04-04 04:44:44', '2018-04-04 04:44:44');
-- Query OK, 1 row affected (0.00 sec)
```

### MySQLの中から、追加されているレコードを確認してみる

```sql
SELECT * FROM users;
-- +----+------+---------------+------+------+---------+------------+---------+---------------------+---------------------+
-- | id | name | email         | sex  | age  | address | attendance | opinion | created_at          | updated_at          |
-- +----+------+---------------+------+------+---------+------------+---------+---------------------+---------------------+
-- |  1 | foo  | foo@gmail.com |    1 |   23 |       2 |          0 | foooo   | 2017-04-04 04:44:44 | 2018-04-04 04:44:44 |
-- +----+------+---------------+------+------+---------+------------+---------+---------------------+---------------------+
```

### rails console から新たにレコードを追加する

```rb
user = User.create(name: "taro", email: "val@gmail.com", sex: 0, address: 1, attendance: 1, opinion: 'nothing special')

#user.saveでDBに保存する
user.save
# => true
```

### Rails Console上でレコード取得

```rb
User.all # get all users from record
User.all.last # get last added user from record

first_user = User.all.first # get all users and then get the first user
first_user.destroy # delete the first user

user = User.all.first
user.name = "ichitaro" # overwrite the name with 'ichitaro
user.save # save the data

User.all.count # count the number of all users
User.find_by(id:2) # get a user with id:2
```

### controller

```rb title=app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
    @users = User.all
   end
end
```

### view

```rb title=app/view/index.html.erb
 <body>
    <h1>Users</h1>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Email</th>
          <th>Sex</th>
          <th>Age</th>
          <th>Address</th>
          <th>Attendance</th>
          <th>Opinion</th>
          <th colspan="3"></th>
        </tr>
      </thead>

      <tbody>
        <% @users.each do |user| %>
          <tr>
            <td><%= user.name %></td>
            <td><%= user.email %></td>
            <td><%= user.sex %></td>
            <td><%= user.age %></td>
            <td><%= user.address %></td>
            <td><%= user.attendance %></td>
            <td><%= user.opinion %></td>
          </tr>
        <% end %>
      </tbody>
    </table>
  </body>
```

```rb title=app/views/layouts/application.html.erb
<!DOCTYPE html>
<html>
  <head>
    # 割愛
  </head>
  <body>
    <%= yield %>
  </body>
</html>
```

ルーティングを変更

```rb title=app/config/routes.erb
resources :users
```

`rails routes` 実行

```rb
# =>
#  Prefix Verb   URI Pattern Controller#Action

#     users GET    /users(.:format)            users#index
#           POST   /users(.:format)            users#create
#   new_user GET    /users/new(.:format)        users#new
# edit_user GET    /users/:id/edit(.:format)   users#edit
#       user GET    /users/:id(.:format)        users#show
#           PATCH  /users/:id(.:format)        users#update
#           PUT    /users/:id(.:format)        users#update
#           DELETE /users/:id(.:format)        users#destroy
# ...
```

次に上にある、「users GET    /users(.:format)  users#index」を実装
UserController の中に show アクションを作成

```rb title=app/controllers/users_controller.rb
class UsersController < ApplicationController
    def index
        @users = User.all
    end
# 下が追加したshowアクション
    def show
    end
end
```

```rb title=app/views/users/index.html.erb
# user.nameのラインを下の様に変更
<td><%= link_to user.name, user_path(user.id) %></td>
# <% link_to ("A"."/B") %>
# 上はhtml上で <a href="/B">A</a>に変化する。
```

この時点で `rails s` で立ち上げると

### showアクション

- users_path は users#index へのリンク
- new_user_path は users#new へのリンク
- edit_user_path は users#edit へのリンク
- user_path は users#show へのリンク

```rb title=app/views/users/show.html.erb
<p id="notice"><%= notice %></p>
<p>
  <strong>Name:</strong>
  <%= @user.name %>
</p>
<p>
  <strong>Email:</strong>
  <%= @user.email %>
</p>
# 割愛

<%= link_to 'Edit', edit_user_path(@user) %> |
<%= link_to 'Back', users_path %>
```

### show, edit アクションの定義

```rb title=app/controllers/users_controller.rb
def show
  @user = User.find params[:id]
end

def edit
  @user = User.find(params[:id])
end
```

```html title=app/views/users/edit.html.erb
<%= form_with(model: @user, local: true) do |form| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this @user from being saved:</h2>
      <ul>
        <% @user.errors.full_messages.each do |message| %>
          <li><%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>
  <div class="field">
    <%= form.label :name %>
    <%= form.text_field :name %>
  </div>
  <div class="field">
    <%= form.label :email %>
    <%= form.text_field :email %>
  </div>
  <div class="field">
    <%= form.label :sex %>
    <%= form.number_field :sex %>
  </div>
  # 割愛
<% end %>
```

### 追加：2日目を参考にし、表示を触ってみる

性別の値 0 or 1 を男性 or 女性で表示させる。

```rb title=app/models/user.rb
class User < ApplicationRecord
  enum sex: { male: 0 ,female: 1}
end
```

男性、女性で表示されるようになった。だが、edit ページは、テキスト入力のまま

ラジオボタンに変更

```html title=app/views/users/edit.html.erb
<div class="field">
    <%= form.label :sex %>
    <%= form.radio_button :sex, 'male' %>男性
    <%= form.radio_button :sex, 'female' %>女性
</div>
```

同様に、年齢、住所、参加不参加もラジオボタンにしておく。

```rb
user = User.find_by(name:"foo") # get a user named 'foo'
user.age = 0 # rewrite the user's data about age with 0
user.save
User.find_by(name: "foo") # confirm
# =>
# <User id: 1, name: "foo", email: "foo@gmail.com", sex: "女性", age: 0, address: "日本以外", attendance: "参加", opinion: "foooo", created_at: "2017-04-04 04:44:44", updated_at: "2019-03-08 00:07:47"
```

### 年齢のラジオボタンを追加

---

## 8日目

### users_controller

```rb title=app/controllers/uupdate.rb
def update
    @user = User.find params[:id]
    if @user.update(params.require(:user).permit(:name, :email, :sex, :age, :address, :attendance, :opinion))
      respond_to do |format|
        format.html { redirect_to @user, notice: 'User was successfully updated.' }
      end
    else
      respond_to do |format|
        format.html { render :edit }
      end
    end
end

def destroy
  @user = User.find params[:id]
  @user.destroy
  respond_to do |format|
    format.html { redirect_to users_url, notice: 'User was successfully destroyed.' }
  end
end

def new
  @user = User.new
end
```

### indexページからのdestroyへのリンク作成

```html title=app/views/users/index.html.erb
<td><%= link_to 'Destroy', user, method: :delete, data: { confirm: 'Are you sure?' } %></td>
```

### newページ編集

```html title=app/views/users/new.html.erb
<h1>New User</h1>
<%= form_with(model: @user, local: true) do |form| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this user from being saved:</h2>
      <ul>
      <% @user.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
  <div class="field">
    <%= form.label :name %>
    <%= form.text_field :name %>
  </div>
  <div class="field">
    <%= form.label :email %>
    <%= form.text_field :email %>
  </div>
  # 割愛
  
<% end %>
<%= link_to 'Back', users_path %>
```

### indexからnewへのリンク作成

```rb title=app/views/users/index.html.erb
<%= link_to 'New User', new_user_path %>
```

### createアクション定義

```rb title=app/controllers/users_controller.rb
def create
    @user = User.new(params.require(:user).permit(:name, :email, :sex, :age, :address, :attendance, :opinion))
    if @user.save
      respond_to do |format|
        format.html { redirect_to @user, notice: 'User was successfully updated.' }
      end
    else
    respond_to do |format|
      format.html { render :edit }
    end
  end
end
```

## リファクタリング

> wikipedia
>> リファクタリング (refactoring) とは、コンピュータプログラミングにおいて、プログラムの外部から見た動作を変えずにソースコードの内部構造を整理すること

### createアクションとupdateアクションの共通化

2 アクションに下の共通箇所がある

```rb title=app/controllers/users_controller.rb
@user = User.new(params.require(:user).permit(:name, :email, :sex, :age, :address, :attendance, :opinion))
```

リファクタリング後

```rb title=app/controllers/users_controller.rb
def create
  @user = User.new(user_params)
  if @user.save
    respond_to do |format|
      format.html { redirect_to @user, notice: 'User was successfully updated.' }
    end
  else
    respond_to do |format|
      format.html { render :edit }
    end
  end
end

def update
  @user = User.find params[:id]
  if @user.update(user_params)
    respond_to do |format|
      format.html { redirect_to @user, notice: 'User was successfully updated.' }
    end
  else
    respond_to do |format|
      format.html { render :edit }
    end
  end
end

private

def user_params
    params.require(:user).permit(:name, :email, :sex, :age, :address, :attendance, :opinion)
  end
end
```

### show. edit. updata, destroyの共通化

```rb title=app/controllers/users_controller.rb
# 共通している部分
@user = User.find params[:id]

# 共通化した結果
# set_user追加
def set_user
  @user = User.find params[:id]
end

# before_action追加
# show, edit, update, destroyアクションの前に、必ず実行の意
class UsersController < ApplicationController
  before_action :set_user, only: [:show, :edit, :update, :destroy]
```

### アクションのリファクタリング後（全体）

```rb title=app/controllers/users_controller.rb
class UsersController < ApplicationController
  before_action :set_user, only: [:show, :edit, :update, :destroy]

def index
    @users = User.all
end
def show; end
def edit; end
def update
  if @user.update(user_params)
    respond_to do |format|
    format.html { redirect_to @user, notice: 'User was successfully updated.' }
  end
  else
    respond_to do |format|
      format.html { render :edit }
    end
  end
end
def destroy
  @user.destroy
  respond_to do |format|
    format.html { redirect_to users_url, notice: 'User was successfully destroyed.' }
  end
end
def new
  @user = User.new
end
def create
  @user = User.new(user_params)
  if @user.save
    respond_to do |format|
      format.html { redirect_to @user, notice: 'User was successfully updated.' }
    end
  else
    respond_to do |format|
      format.html { render :edit }
    end
  end
end

private
  def set_user
    @user = User.find params[:id]
  end
  def user_params
    params.require(:user).permit(:name, :email, :sex, :age, :address, :attendance, :opinion)
  end
end
```
