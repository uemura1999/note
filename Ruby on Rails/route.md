# Railsのルーティング
## Railsルーターの目的
- 概要
  - 受け取ったURLを認識し、適切なコントローラ内アクションに振り分ける
### URLを実際のコードに振り分ける
- 1.GET /patients/17のHTTPリクエストを受け取る
- 2.特定のコントローラ内アクションを探す
  -  get '/patients/:id, to: 'patients#show'
- 3.patientsコントローラのshowアクションに割り当て
  - paramsに{id:'17'}ハッシュが含まれる
### コードからパスやURLを生成する
- 1.上のルーティングが以下のように変更
  - get '/patients/:id', to: 'patients#show', as: 'patient'
  - /patients/:idのURLに対して、PatientsControllerのshowアクションを呼び出す。また、:idは動的な部分で、実際のリクエスト時に置き換えられる
- 2.アプリケーション内のコントローラ
  - @patient = Patient.find(params[:id])
  - コントローラー内のshowアクションで、リクエストから渡されたIDを使用して、対応するものをdbから検索
  - @patientには、検索された情報が格納されます。
- 3.上記のコントローラに対応すビュー
  - <%= link_to 'Patient Record', patient_path(@patient) %>
  - link_toメソッドを使用して、"Patient Record"というリンクを生成
  - リンク先は、patient_path(@patient)
  - patient_pathは、先程のルーティングで設定された名前であり、:idの部分は@patientのIDで置き換えられる
- まとめ
  - ルーターによって/patients/17というパスが生成
  - これを利用することでビューが改修しやすくなり、コードも読みやすくなる
### Railsルーターを設定する
- config/routes.rbファイルのルーティング

## リソースベースのルーティング
- 概要
  - resourcesを宣言するだけで、コントローラのindex, show, new, edit, create, update, destroyアクション全てを宣言できる
  - ブラウザはRailsに対してリクエストを送信する際に、特定のHTTPメソッドを使ってURLに対するリクエストを作成する
### Web上のリソース
- 1.DELETE /photos/17のHTTPリクエストを受け取る
- 2.特定のコントローラ内アクションを探す
  - resources :photos(ルーティング)
- 3.photosコントローラ内のdestroyアクションに割り当てる
  - paramsハッシュに{ id: '17'}を含める
### CRUD , verb , アクション
- RESTfulルーティング
  - HTTP verbと、コントローラ内アクションを指すURLが対応付けられる
  - １つのアクションは、db上での特定のCRUD(Create/Read/Update/Delete)操作に対応付けられる
- resources :で作成される７つのルーティング
| HTTP verb | パス | コントローラ#アクション | 目的 |
| GET | /photos | photos#index | 全ての写真を一覧表示 |
| GET | /photos/new | photos#new | 写真を１つ作るためのHTMLファイルを返す |
| POST | /photos | photos#create | 写真を１つ作成 |
| GET | /photos/:id | photos#show | 特定の写真を表示する |
| GET | /photos/:id/edit | photos#edit | 写真編集用のHTMLフォームを1つ返す |
| PATCH/PUT | /photos/:id | photos#update | 特定の写真を更新する |
| DELETE | /photos/:id | photos#destroy| 特定の写真を削除する |
### パスとURL用ヘルパー
- 1.概要
  - RESTfulルーティングを作成すると、コントローラで多くのヘルパーが利用可能
- 2.resorces :photosの例
 photos_pathは、/photosを返す
 new_photo_pathは、/photos/newを返す
 edit_photo_path(:id)は、/photos/:id/editを返す
 photo_path(:id)は/photos/:idを返す