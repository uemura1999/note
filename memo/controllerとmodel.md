## ViewからControllerに送信された値が、Modelを通してDBに保存されるまでの流れ
- コントローラ内アクションがユーザーからの入力やリクエストを受け取る
  - doctorsリソースのcreateアクションを実行
    - resources :doctors, only: %i[create]
- コントローラがモデルにデータの操作を指示する
  - モデルのインスタンスを作成する（オブジェクトの作成）
  ```rb
  def create
  @doctor = Doctor.create(doctors_params)
  end
  ```
  - データベースに保存する
  ```rb
  if @doctor.save
    render api_response(true, { redirect_url: doctor_result_url })
    else
    flash[:error] = '回答結果の保存に失敗しました。'
    end
  end
  ```
  - ストロングパラメータの設定
  ```rb
  private
  def doctor_params
    params.require(:doctors).permit(:question)
  end
  ```
モデルがDBにアクセスして操作(クエリ)を発行する
- doctors_controllerのcreateアクションが作動し、saveが実行されるとSQL文(クエリ)を発行する
DBが操作を実行し、結果をモデルに返す
- ActiveRecordを使用してDBからデータを取得する
モデルが結果をコントローラに返す

##  アンケート結果を格納するモデルを作成する（事前準備）
- Modelとマイグレーションファイルの作成
  ```rails g model User　question:integer```
- マイグレーションの実行
  ```rails db:migrate```