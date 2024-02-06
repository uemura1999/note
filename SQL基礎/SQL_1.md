##  データを取得しよう
- データベース(DB)とは？
  - テキストや数値などのデータを保存するための仕組み
  - 必要に応じて複数のテーブルを作成することができる
    - 表：テーブル
    - 行：レコード
    - 列：カラム
- クエリとは？
  - DBに送る命令のこと
    - SELECT
      - どのカラムのデータを取得するか
    - FROM
      - どのテーブルのカラムか
   ```sql
   SELECT name
   From purchases;
   ```
- クエリの書き方
  - 複数のカラムからデータを取得する
    - コンマで区切る
    ```sql
    SELECT name, price
    FROM purchases;
    ```
  - すべてのカラムからデータを取得する
    - `*`を使う
    ```sql
    SELECT *
    FROM purchases;
    ```
##  特定のデータを取得しよう
- 特定のデータを取得する
  - WHERE
    - 〇〇カラムが〇〇であるレコードを取得する
      - テキストデータはダブル(シングル)クォーテーションを使う
      - 数値データはクォーテーションを使わない
      - 日付データはダブル(シングル)クォーテーションを使う
    ```sql
    SELECT *
    FROM purchases
    WHERE category = '食費';
    ```
### 演算子を使ってデータを取得しよう
- 比較演算子
  - WHERE price > 1000
    - priceカラムが1000以上であるレコードを取得する
  - WHERE purchased_at <= "2017-08-01"
    - purchased_atカラムが2017-08-01以前のデータを検索する
- LIKE演算子
  - WHERE name LIKE 文字列;
    - 指定したカラムが〇〇を含むレコード
  - ワイルドカード
    - %：どんな文字列にも一致することを指す記号
      - WHERE name LIKE '%りんご%';
        - りんごを含むレコードを取得する
    - 前方一致
      - WHERE name LIKE 'りんご%';
        - りんごから始まるレコードを取得する
    - 後方一致
      - WHERE name LIKE '%りんご';
        - りんごで終わるレコードを取得する
- NOT演算子
  - WHERE NOT price > 1000;
    - priceカラムが1000以下のレコードを取得する
  - WHERE NOT name LIKE '%りんご%';
    - りんごを含まないレコードを取得する
- NULL
  - WHERE name IS NULL;
    - nameカラムがNULLのレコードを取得する
  - WHERE name IS NOT NULL;
    - nameカラムがNULLでないレコードを取得する
- AND・OR演算子
  - AND演算子
    - 条件1と条件2の両方を満たすレコードを取得する
      - WHERE 条件1 AND 条件2;
  - OR演算子
    - 条件1または条件2のいずれかを満たすレコードを取得する
      - WHERE 条件1 OR 条件2;
    ```sql
    WHERE character = "ひつじ"
    AND category = "食費";
    OR character_name = "ねこ"
    ```
##  取得結果を加工しよう
- データを並び替える
  - ORDER BY 並べたいカラム名 並べ方;
    - デフォルトは昇順(ASC)
    - 降順にする場合はDESCを使う
    ```sql
    SELECT *
    FROM purchases
    ORDER BY price DESC;
    ```
- 最大で何件を取得するかを指定する
  - LIMIT データの件数;
    - 件数分のレコードを取得する
    - ORDER BYとLIMITを併用することも可能
    ```sql
    SELECT *
    FROM purchases
    LIMIT 5;
    ```
