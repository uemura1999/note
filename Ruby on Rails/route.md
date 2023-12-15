# 1.routeとは
- 概要
　- 受け取ったURLを認識し、適切なコントローラ内アクションに振り分ける（パスやURLの生成も可能）
## 1.1 URLを実際のコードに振り分ける
- GET /patients/17のHTTPリクエストを受け取る
　1.特定のコントローラ内アクションを探す(get '/patients/:id, to: 'patients#show')
　2.patientsコントローラのshowアクションに割り当てられ、paramsに{id:'17'}ハッシュが含まれる
## 1.2 コードからパスやURLを生成する
- routes
　- get '/patients/:id', to: 'patients#show', as: 'patient'
　- /patients/:idのURLに対して、PatientsControllerのshowアクションを呼び出す。また、:idは動的な部分で、実際のリクエスト時に置き換えられる
- controller
　- @patient = Patient.find(params[:id])
　- コントローラー内のshowアクションで、リクエストから渡されたIDを使用して、対応するものをデータベースから検索しています。@patientには、検索された情報が格納されます。
- view
　- <%= link_to 'Patient Record', patient_path(@patient) %>
　- link_toメソッドを使用して、"Patient Record"というリンクを生成しています。リンク先は、patient_path(@patient)です。patient_pathは、先程のルーティングで設定された名前であり、:idの部分は@patientのIDで置き換えられます。
- まとめ
ルーターによって/patients/17というパスが生成されます。これを利用することでビューが改修しやすくなり、コードも読みやすくなります。このidはルーティングヘルパーで指定する必要がない点にご注目ください。
## 1.3 Railsルーターを設定する
- ルーティングはconfig/routes.rbファイルの中に存在する
　- Rails.application.routes.draw do
    resources :brands, only: [:index, :show] do
    resources :products, only: [:index, :show]
  end
    resource :basket, only: [:show, :update, :destroy]
    resolve("Basket") { route_for(:basket) }
   end
# 2.リソースベースのルーティング
- 概要
　- resourcesを宣言するだけで、コントローラのindex, show, new, edit, create, update, destroyアクション全てを宣言できる
## 2.1 Web上のリソース
- 概要
　- ブラウザはRailsに対してリクエストを送信する際に、特定のHTTPメソッドを使ってURLに対するリクエストを作成する
- DELETE /photos/17のHTTPリクエストを受け取る
　1.特定のコントローラ内アクションを探す(resources :photos)
　2.photosコントローラ内のdestroyアクションに割り当て、paramsハッシュに{ id: '17'}を含める
## 2.2 CRUD , verb , アクション
- リソースフルルーティングの考え
　1.各種HTTP verbと、コントローラ内アクションを指すURLが対応付けられる
　2.１つのアクションは、db上での特定のCRUD(Create/Read/Update/Delete)操作に対応付ける
- resources :で作成される７つのルーティング
　- GET            /photos      photos#index
　　GET           /photos/new   photos#new
　　POST         /photos        photos#create
　　GET　       /photos/:id     photos#show
　　GET        /photos/:id/edit photos#edit
　　PATCH/PUT /photos/:id       photos#update
　　DELETE   /photos/:id        photos#destroy
## 2.3 パスとURL用ヘルパー
- 概要
　- リソースフルなルーティングを作成すると、コントローラで多くのヘルパーが利用できる
- resorces :photosの例
　- photos_pathは、/photosを返す
　- new_photo_pathは、/photos/newを返す
　- edit_photo_path(:id)は、/photos/:id/editを返す
　- photo_path(:id)は/photos/:idを返す