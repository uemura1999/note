##  業務の流れからER図を作成
- どのような業務があるのかを洗い出す
  - パーソナリティ診断の例
    1. 回答者のユーザー登録を行う
      - 名前と利用者IDを登録する
    2. 利用者IDを入力した回答ページを表示する
      - 利用者IDは、上村社長のスプシで管理されている
    3. 回答者が質問に答える
      - Step1では10個の質問(personality1つに1問)が用意されている
      - Step3では、質問が増える(personality1つにX問)ことを想定している
    4. 回答が終わったら、自動で結果ページに遷移する
      - DBの中に、回答の数値が保存される
      - Step2では、回答数値を簡単に見ることができる
    5. 上村社長が診断が正しいか結果ページを見ながらヒアリングする
      - ヒアリング情報は、利用者IDと紐づけてスプシに保存される
- 取得する予定のすべての情報を洗い出す
  - 名前
  - 利用者ID
  - 質問
  - パーソナリティ名
  - 説明
  - 特徴
  - 回答数値
- 重複したデータが入っている物を探し、一言で言い表されるものを分類する
  - User
    - 名前
    - 利用者ID
    - | ID | 名前 | 利用者ID |
  - Question
    - 質問
    - | ID | title | body |
  - Personality
    - パーソナリティ名
    - 説明
    - 特徴
    - | ID | name | description | feature |
  - Answer
    - 回答数値
- 変わらない値でテーブル同士をつなぐ
  - UserとQuestionをAnswerでつなぐ
    - UserとQuestion, Answerの関係
      - Userは、複数のAnswerを持つ（１対多）
      - Questionは、複数のAnswerを持つ（１対多）
    - 正規化する
      - | ID | user_id | question_id | answer_value |
  - QuestionとPersonalityをつなぐ
    - QuestionとPersonalityの関係
      - Step1
        - Questionは、1つのPersonalityを持つ（１対１）
        - Personalityは、1つのQuestionを持つ（１対1）
      - Step2
        - Questionは、複数のPersonalityを持つ（１対多）
        - Personalityは、複数のQuestionを持つ（１対多）
    - 正規化する（最初の段階からStep2で考える）
      - | ID | title | body | personality_id |
  - ER図を作成する
    - UserとAnswerを１対多でつなぐ
    - QuestionとAnswerを１対多でつなぐ
    - PersonalityとQuestionを１対多でつなぐ
  - ER図が正しいか検証する

### 上村の反省
- 業務を考慮せずに、ER図を作成してしまった
  - 中間テーブルを使うかどうかを関係性だけで考えてしまったため、業務で必要かを判断する
- 検証の際に１つものしか見ていなかった
  - あらゆる可能性を考慮して考えることが大事
- 業務とプログラミンを一緒に考えられるようになることを目指す
