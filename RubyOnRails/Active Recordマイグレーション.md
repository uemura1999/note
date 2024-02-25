# Active Recordマイグレーション
- SQL作成の必要がなく、スキーマ(設計図)や変更がDBに依存しない
  - スキーマは空の状態から始まり、マイグレーションによる変更が加わるたびに、テーブル、カラム、エントリが追加/削除される
- マイグレーションの例
  - productsという名前のテーブルが追加される
    - name(stringカラム)とdescription(textカラム)が追加
  - 主キーはidという名前で暗黙に追加される
  - timestampは、created_atとupdated_atを追加
  ```rb
  class CreateProducts < ActiveRecord::Migration[7.1]
  def change
    create_table :products do |t|
      t.string :name
      t.text :description

      t.timestamps
      end
    end
  end
  ```
##  マイグレーションを生成する
- タイムスタンプを自動生成するジェネレータ
  - bin/rails generate migration クラス名を実行
  - 上を実行すると、空のマイグレーションが適切な名前で作成
  ```rb
  class クラス名 < ActiveRecord::Migration[7.1]
    def change
    end
  end
  ```
- ファイルの保存先
  - YYYYMMDDHHMMSS_create_products.rbのファイル名でdb/migrateディレクトリに保存される
- クラス名
  - マイグレーションのクラス名(CamelCase)は、ファイル名の後半(snake_case)と一致する必要がある
    - 20080906120000_create_products.rbではCreateProductsというクラスを定義
##  新しいカラムを追加する
- カラム名や型を後ろに続けて、同時に作成する
  - マイグレーション名が'AddColumnToTable'や'RemoveColumnFromTable'
  - その後ろにカラム名や型が続く形式
- マイグレーションファイルが作成
  - bin/rails generate migration AddPartNumberToProducts part_number:string
  ```rb
  class AddPartNumberToProducts < ActiveRecord::Migration[7.1]
    def change
      add_column :products, :part_number, :string
      # add_column：指定したテーブルにカラムを追加
    end
  end
  ```
- 新しいカラムにインデックスも追加したい場合
  - bin/rails generate migration AddPartNumberToProducts part_number:string:index
  ```rb
  class AddPartNumberToProducts < ActiveRecord::Migration[7.1]
    def change
      add_column :products, :part_number, :string
      add_index :products, :part_number
      # add_index：テーブルの中の特定のカラムのデータを複製し検索が行いやすいようにする
    end
  end
  ```
- 自動生成されるカラムを2個以上にするには
  - $ bin/rails generate migration AddDetailsToProducts part_number:string price:decimal
  ```rb
  class AddDetailsToProducts < ActiveRecord::Migration[7.1]
    def change
      add_column :products, :part_number, :string
      add_column :products, :price, :decimal
    end
  end
  ```
##  カラムを削除する
- カラムを削除するマイグレーションをコマンドラインで生成
  - $ bin/rails generate migration RemovePartNumberFromProducts part_number:string
  ```rb
  class RemovePartNumberFromProducts < ActiveRecord::Migration[7.1]
    def change
      remove_column :products, :part_number, :string
    end
  end
  ```
##  新しいテーブルを作成する
- マイグレーション名が、Create✗✗✗で、その後にカラム名と型が続く場合は、✗✗✗という名前のテーブルが作成され、指定の型のカラム名がその中に生成される。
  - $ bin/rails generate migration CreateProducts name:string part_number:string
  ```rb
  class CreateProducts < ActiveRecord::Migration[7.1]
    def change
      create_table :products do |t|
        t.string :name
        t.string :part_number

       t.timestamps
     end
    end
  end
  ```
