---
create: '2019-03-29'
update: '2019-12-04'
title: 'Day 16：devise で管理者を追加する'
tags: [rails, devise]
published: true
---

- [Qiita: deviseだけで管理者を追加したい](https://qiita.com/OriverK/items/d7704d23cf74c51503b4)

## Environment

- Ubuntu 18.04
  - Ruby：2.51
  - Rails: 5.2.2
    - gem
      - `devise`: ログインなどの機能用
      - `kaminari: pagination
  - DB: PostgreSQL

## 実作業

### テーブルにadminカラム追加

```sh
# rails g migration Addカラム名Toテーブル名　カラム定義
rails g migration AddAdminToStudent admin:boolean
```

#### boolean型

真理値の「真 = true」と「偽 = false」という 2 値をとるデータ型のこと。Ruby では偽は false と nil で、それ以外が true になる。言語や DB によっては、1 と 0 だったり、違うので注意。

### マイグレーションファイルを編集

boolean 型と定義する際は、デフォルト値を設定しないといけない。admin のデフォルト値に引数 false を渡し、デフォルトでは admin 権限がない、と指定する。

```rb title=/db/migrate/20190328011407_add_admin_to_student.rb
class AddAdminToStudent < ActiveRecord::Migration[5.2]
  def change
    add_column :students, :admin, :boolean,default: false
  end
end
```

`rails db:migrate`

### admin権限を確認付与

```rb
stu = Student.find(1)
stu.admin?
=>false
stu.admin = true
stu.save
stu.admin?
=>true
```

admin 属性が追加され、また admin?メソッドを使用できるようになっている。

### adminのみが全データを見られるようにする

admin 以外は、自分のデータしか見られないようにしたい。

```rb title=users_controller.rb
 def index
    if current_student.admin?
      @students = Student.page params[:page]
    else
      @student = current_student
    end
  end
```

```html title=app/views/student.html.erb
 <tbody>
    <% if current_student.admin? %>
      <% @students.each do |student| %>
        <tr>
          <td><%= student.name %></td>
          <td><%= student.email %></td>
          <td><%= display_name student.gender %></td>
          <td><%= display_age student.age %></td>
          <td><%= student.opinion %></td>
          <td><%= link_to 'Show', student %></td>
          <td><%= link_to 'Edit', edit_student_path(student) %></td>
          <td><%= link_to 'Destroy', student, method: :delete, data: { confirm: 'Are you sure?' } %></td>
          <td><%= link_to 'New Exam Result', new_exam_result_path(student_id: student.id) %></td>
          <td><%= link_to 'New Club Student', new_club_student_path(student_id: student.id) %></td>
        </tr>
      <% end %>
    <% else %>
      <tr>
        <td><%= @student.name %></td>
        <td><%= @student.email %></td>
        <td><%= display_name @student.gender %></td>
        <td><%= display_age @student.age %></td>
        <td><%= @student.opinion %></td>
        <td><%= link_to 'Show', @student %></td>
        <td><%= link_to 'Edit', edit_student_path(@student) %></td>
        <td><%= link_to 'Destroy', @student, method: :delete, data: { confirm: 'Are you sure?' } %></td>
        <td><%= link_to 'New Exam Result', new_exam_result_path(student_id: @student.id) %></td>
        <td><%= link_to 'New Club student', new_club_student_path(student_id: @student.id) %></td>
      </tr>
    <% end %>
  </tbody>
</table>
<% if current_student.admin? %>
  <%= paginate @students %>
<% end %>
```
