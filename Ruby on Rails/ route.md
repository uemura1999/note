# Railsルーターの目的
- 概要
 - ユーザーから送られたHTTPリクエストとURLを元に、ルーティングを確認して、どのコントローラーのどのアクションを実行するか決定する
## トップページを指定する
- root "dashboards#index"
 - rootはトップページのこと
 - ドメイン名の後ろに何もついていない状態のものがrootURL
## ルーティングの流れ
1. RailsがHTTPリクエストを受け取る
- GET /patients/17
 - GET：何かを要求している(ページの表示や移動)
 - POST：こちらから送る(値を作る＋新規データ作成)
2. config/routes.rbを確認
- get '/patients/:id', to: 'patients#show'
 - コントローラのshowアクションに割り当てられる
 - paramsに{ id: '17' }ハッシュが含まれる
## コードからパスやURLを生成
1. ルーティングヘルパーを生成
- get '/patients/:id', to: 'patients#show', as: 'patient'
 - コントローラのshowアクションに割り当てられる
 - patient_pathというルーティングヘルパーを作成
2. ルーティング
- @patient = Patient.find(params[:id])
 - idをデータベースから検索する
3. 動的に生成されたパスを返す
- <%= link_to 'Patient Record', patient_path(@patient) %>
 - 動的な部分(:id)を自動で埋め込んでくれる
 - ルーターによって/patients/17というパスが生成
## Railsルータの設定
```
Rails.application.routes.draw do
  resources :brands, only: [:index, :show] do
    resources :products, only: [:index, :show]
end
```
# resourcesを使った書き方
- 概要
 - コントローラのindex, show, new, edit, create, update, destroyアクション全てを宣言できる
## Web上のリソース
1. RailsがHTTPリクエストを受け取る
- DELETE /photos/17
2. 特定のコントローラ内アクションを探す
- resources :photos
3. コントローラ内での割当て
- photosコントローラのdestroyアクションに割り当てる
- paramsハッシュに{ id: '17'}を含める
## CRUD , verb , アクション
- リソースフルなルーティング
　- HTTP verbと、コントローラ内アクションを指すURLが対応付けられる
　- １つのアクションは、db上での特定のCRUD(Create/Read/Update/Delete)操作に対応付けられる
- resourcesで作成される７つのルーティング
| HTTP verb | パス | コントローラ#アクション | 目的 |
| GET | /photos | photos#index | 全ての写真を一覧表示 |
| GET | /photos/new | photos#new | 写真を１つ作るためのHTMLファイルを返す |
| POST | /photos | photos#create | 写真を１つ作成 |
| GET | /photos/:id | photos#show | 特定の写真を表示する |
| GET | /photos/:id/edit | photos#edit | 写真編集用のHTMLフォームを1つ返す |
| PATCH/PUT | /photos/:id | photos#update | 特定の写真を更新する |
| DELETE | /photos/:id | photos#destroy| 特定の写真を削除する |
## パスとURL用ヘルパー
- 1.概要
 - RESTfulルーティングを作成すると、コントローラで多くのヘルパーが利用可能
 - URL用ヘルパー
  - リソース名から変数名が自動的に生成され、その変数を利用することで、URLを取得できる
- 2.resorces :photosの例
| ヘルパー名称 | 得られるURL |
| リソース名_path | /リソース名 |
| new_リソース名_path | /リソース名/new |
| edit_リソース名_path(:id) | /リソース名/:id/edit |
| リソース名_path(:id) | /リソース名/:id |



##### 参考資料
- https://railsguides.jp/routing.html
- https://programming-i.net/rails-routing
- https://docs.google.com/document/d/1WcR-ucolWZXaQtHd7lsx06bK57-wuQzamM8md0COcHM/edit?usp=sharing
- https://web-camp.io/magazine/archives/16815