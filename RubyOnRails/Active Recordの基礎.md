##  Active Recordの基礎
- Active Recordとは？
  - RailsとSQLの翻訳機
  - MVCのM(ビジネスデータとビジネスロジック)にあたる部分
  - DBとの接続処理
    - 恒久的に保存されるビジネスオブジェクトの作成と利用
- O/Rマッピングとは？
  - DBをSQL文で直接操作せずに、Modelを介して操作する技術

##  Active RecordにおけるCoC
- 命名ルール
  - モデルのクラス名を複数形にした名前でDBのテーブル名を探索する
    - 頭文字大文字、単数形、キャメルケース、スネークケース
      - CamelCase：語頭を大文字にしてスペースなしでつなぐ
      - snake_case：小文字とアンダースコアで構成する
- DB構造のルール（スキーマ）
  - 主キー
    - テーブルに必ず1つは必要な存在で、1行のレコードを特定できる
    - デフォルトはidという名前のintegerカラムがテーブルの主キーに使われる
  - 外部キー
    - 2つのテーブル間の列同士で設定するもの
    - デフォルトは「テーブル名の単数形_id」にする
      - itemsテーブルなら、item_id
  - 自動生成されるカラム
    - created_at：作成時のタイムスタンプ
    - updated_at：更新時のタイムスタンプ

##  Active Recordのモデルを作成する
- ApplicationRecordクラスのサブクラスを作成するだけ
  - Productモデルを作成し、DBのproductsテーブルにマッピングされる
  - DB内のレコードやカラムが、アプリケーション内で使われるモデルオブジェクトの属性として扱われる
  ```rb
  class Product(モデル名) < ApplicationRecord
  end
  ```
###  テーブル名の変更方法
- モデルファイルの中に記述すればOK
```rb
class モデル名 < ApplicationRecord
  self.table_name = "変更後のテーブル名"
end
```
###  主キーの変更方法
- モデルファイルで変更できる
- idの名前は主キーのみなので、他のカラムではサポートされない
```rb
class モデル名 < ApplicationRecord
  self.primary_key = "変更後の主キー名"
end
```
##  CRUD
- データ操作の基本となる4つの処理
  - Create：テーブルにレコードを挿入する
  - Read　：テーブルのレコードを抽出する
  - Update：テーブルのレコードを更新する
  - Delete：テーブルのレコードを削除する
###  Create
- モデルのインスタンス生成と同時にDBに保存する
  - ```モデルのクラス名.create(カラム名1: 値1, カラム名2: 値2)```
- newメソッドでインスタンスを作成するとオブジェクトは保存されない
  - 下記の場合、user.saveを実行して初めてDBにレコードがコミットされる
  ```rb
  user = User.new
  user.カラム名1 = "値1"
  user.カラム名2 = "値2"
  ```
- createやnewにブロックを渡すと、そのブロックで初期化された新しいオブジェクトがyieldされる
```rb
user = User.new do |u|
  u.name = "David"
  u.occupation = "Code Artist"
end
```
###  Read
- DBのデータにアクセスできるAPIを提供
  - all：全てのレコードを取得する
    - モデルのクラス名.all
  - find：「ID」に当てはまるレコードを全て取得する
    - モデルのクラス名.find(主キー値)
    - モデルのクラス名.find([主キー値1, 主キー値2])
  - find_by：「条件」を指定して最初の1件を取得する
    - モデルのクラス名.find_by(条件)
    - User.find_by(name: 'tanaka')
  - where：「条件」に当てはまるレコードを全て取得する
    - モデルのクラス名.where(条件)
    - User.where(age: 20)
###  Update
- 既存のレコードを引数に指定するプロパティ値に更新する
  - 更新対象のオブジェクト.update(プロパティ名: 値)
  ```rb
  user = User.find_by(name: 'tanaka')
  user.update(name: 'ueda')
  ```
- 複数のレコードを一度に更新した場合
  - User.update_all "max_login_attempts = 3"
###  Delete
- destroy：規定のレコードを削除する
  - 削除対象のオブジェクト.destroy
- destroy_all：指定条件のレコードと関連しているレコードを全て削除
  - モデルのクラス名.destroy_all
- destroy_by：指定条件を検索して全て削除
  - モデルのクラス名.destroy_by(name:'tanaka')
##  バリデーションの作成
- モデルがDBに書き込まれる前にモデルの状態を検証できる
  - 検証内容は、属性が空でないか、一意かどうか、既にDBにないかなど
```rb
class モデル名 < ApplicationRecord
  validates: :name(カラム名), presence(バリデーションルール)
end
```
##  コールバック
- 概要
  - ModelなどのActive Recordオブジェクトが更新されたり削除されたりして状態が変わるときに、その前後でイベントが発生する。そのイベントで任意の処理を行うことができる。（会員データが削除された時にログを出力する）
- コールバックで気をつけるべきこと
  - modelで設定されるので、controllerからロジックが見えず、開発者が予期しない処理が実行される
  - コールバックを多用したり複雑な処理を行うとファットモデルになる
##  マイグレーション
- マイグレーションファイル(カラムやデータの定義が書かれたテーブルの設計図)を元にテーブル操作を行う仕組み
  - ModelとDBは常時連携しておらず、変更内容を反映するためにはマイグレーションをする必要がある
    - テーブル作成：bin/rails db:migrate
    - ロールバック(戻る)：bin/rails db:rollback
- マイグレーションファイルの例
  - generateコマンドでコントローラを生成すればマイグレーションファイルも自動生成される
  ```rb
  class CreateArticles < ActiveRecord::Migration[6.1]
  def change
    create_table :articles do |t|
      t.string :title
      t.text :body
      t.timestamps
      end
    end
  end
  ```

### 参考資料
- https://qiita.com/shizen-shin/items/d3c3a41ff9b46ebd1436
- https://diveintocode.jp/blogs/Technology/migration
- https://pikawaka.com/curriculums/web-service-development/rails-active-record