# Railsルーターの目的
- 概要
  - ユーザーから送られたHTTPリクエストとURLを元に、ルーティングを確認して、どのコントローラーのどのアクションを実行するか決定する
## トップページを指定する
- root "dashboards#index"
  - rootはトップページのこと
  - ドメイン名の後ろに何もついていない状態のものがrootURL
## ルーティングの流れ
-  RailsがHTTPリクエストを受け取る
  - GET /patients/17
    - GET：何かを要求している(ページの表示や移動)
    - POST：こちらから送る(値を作る＋新規データ作成)
- config/routes.rbを確認
  - get '/patients/:id', to: 'patients#show'
    - コントローラのshowアクションに割り当てられる
    - paramsに{ id: '17' }ハッシュが含まれる
## コードからパスやURLを生成
- ルーティングヘルパーを生成
  - get '/patients/:id', to: 'patients#show', as: 'patient'
    - コントローラのshowアクションに割り当てられる
    - patient_pathというルーティングヘルパーを作成
    - patient_urlというルーティングヘルパーも作成
- ルーティング
  - @patient = Patient.find(params[:id])
    - 受け取ったパラメータのidで、データベースからidが合致するPatientを検索する
- 動的に生成されたパスを返す
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
## resourcesを使った書き方
- 概要
  - コントローラのindex, show, new, edit, create, update, destroyアクション全てを宣言できる
## Web上のリソース
- RailsがHTTPリクエストを受け取る
  - DELETE /photos/17
- 特定のコントローラ内アクションを探す
  - resources :photos
- コントローラ内での割当て
  - photosコントローラのdestroyアクションに割り当てる
  - paramsハッシュに{ id: '17'}を含める
## CRUD , verb , アクション
- リソースフルなルーティング
  - HTTP verbと、コントローラ内アクションを指すURLが対応付けられる
    - HTTP verbについては、 HTTPリクエストメソッド(624f2d140b) を参照
  - １つのアクションは、db上での特定のCRUD(Create/Read/Update/Delete)操作に対応付けられる
- resourcesで作成される７つのルーティング

| HTTP verb | パス | コントローラ#アクション | 目的 |
|---|---|---|---|
| GET | /photos | photos#index | 全ての写真を一覧表示 |
| GET | /photos/new | photos#new | 写真を１つ作るためのHTMLファイルを返す |
| POST | /photos | photos#create | 写真を１つ作成 |
| GET | /photos/:id | photos#show | 特定の写真を表示する |
| GET | /photos/:id/edit | photos#edit | 写真編集用のHTMLフォームを1つ返す |
| PATCH/PUT | /photos/:id | photos#update | 特定の写真を更新する |
| DELETE | /photos/:id | photos#destroy| 特定の写真を削除する |
## パスとURL用ヘルパー
- 概要
  - RESTfulルーティングを作成すると、コントローラで多くのヘルパーが利用可能
  - URL用ヘルパー
    - リソース名から変数名が自動的に生成され、その変数を利用することで、URLを取得できる
- resorces :photosの例

| ヘルパー名称 | 得られるURL |
|---|---|
| リソース名_path | /リソース名 |
| new_リソース名_path | /リソース名/new |
| edit_リソース名_path(:id) | /リソース名/:id/edit |
| リソース名_path(:id) | /リソース名/:id |
##  複数のリソースを同時に定義する
- resources :photos, :books, :videos
  - リソースを一度に定義することができる
##  単数形リソース
- 他のユーザーidを参照する必要がない場合
  - 単数形リソースを使ってshowアクションに/profileを割り当てる
    - get 'profile', to: 'users#show'
- Symbolを使う場合は、to:をacton:に置き換える
  - get 'profile', action: :show, controller: 'users'
    - #なしのStringを使う場合は、to:をcontroller:に置き換える
    - (to:の引数にStringを渡す場合はコントローラ#アクションが前提)
### ヘルパーメソッド
- 単数形のリソースフルなルーティングを使うと以下のヘルパーメソッドが生成
  - new_geocoder_pathは/geocoder/newを返す
  - edit_geocoder_pathは/geocoder/editを返す
  - geocoder_pathは/geocoderを返す
##  コントローラの名前空間とルーティング
- 名前空間とは？
  - 決めた名前でルーティングのグループ分けをするための仕組み
  - 最もよく使うのは多数の管理用コントローラ郡をまとめるAdmin::
  - app/controllers/adminディレクトリに配置する
  ```
  namespace :admin do
    resources :articles, :comments
  end
  ```
##  ネストしたリソース
- 親子関係をルーティングで表せる
  - /magazines/:magazine_id/ads
    - resources :magazines do
       resources :ads
      end
- ルーティングヘルパー
  - magazine_ads_url
  - edit_magazine_ad_path

##  ルーティングの「concern」機能
- 他のリソースやルーティング内で使い回せる共通のルーティングを宣言できる
  ```
  concern :commentable do
    resources :comments
  end
  concern :image_attachable do
    resources :images, only: :index
  end
  ```
  ```上のコードと同様
  resources :messages, concerns: :commentable
  resources :articles, concerns: [:commentable, :image_attachable]
  ```
- 複数形のconcernsは、ルーティング内のどの場所にでも配置できる
  - namespaceブロックでの使用例
    - namespace :articles do
       concerns :commentable
      end
- ネスティング回数の上限
  - ネストしたリソースの中で別のリソースをネストできる
  - リソースのネスティングは1回にとどめる必要がある
##  オブジェクトからパスとURLを作成する
```以下のルーティングを活用
resources :magazines do
  resources :ads
end
```
- magazine_ad_pathを使う場合
  - idを数字で渡す代わりにMagazineとAdのインスタンスを引数として渡せる
   - <%= link_to 'Ad details', magazine_ad_path(@magazine, @ad) %>
- 配列からURLを作成する方法
  - url_forを使うことで複数のオブジェクトでもルーティングが行われる
  - <%= link_to 'Ad details', url_for([@magazine, @ad]) %>
  - link_toのヘルパーのみでもオブジェクトを渡せる
  - <%= link_to 'Ad details', [@magazine, @ad] %>
- １冊の雑誌だけにリンクしたい場合
  - <%= link_to 'Magazine details', @magazine %>
- それ以外のアクションについて
  - 配列の最初の要素にアクション名を挿入するだけで済む
  - <%= link_to 'Edit Ad', [:edit, @magazine, @ad] %>
##  RESTfulなアクションをさらに追加する
- メンバールーティングを追加する
  - GETリクエストと/photos/1/previewを認識する
  - リクエストをPhotosコントローラのpreviewアクションにルーティング
  - リソースid値をparams[:id]に渡す
  - preview_photo_urlとpreview_photo_pathヘルパーを作成
  ```menberブロックをリソースブロックに1つ追加する
  resources :photos do
    member do
      get 'preview'
    end
  end
  ```
- HTTP verbをルーティング名ごとに指定する
  - 指定可能なのは、get, patch, put, post, delete
  - memberルーティングが１つしかない場合は省略できる
  - リソースidの値の取得にparams[:id]ではなくparamas[:photo_id]を使う
  - preview_photo_urlがphoto_preview_urlに、preview_photo_pathがphoto_preview_pathにリネームされる
  ```:onオプションを指定する
  resources :photos do
    get 'preview', on: :member
  end
  ```
- コレクションルーティングを追加する
  - GET+/photos/search(idを伴わない)パスを認識する
  - photosコントローラのsearchアクションにルーティングする
  - search_photos_urlやsearch_photos_pathが作成される
  ```collectionブロックを使う
  resources :photos do
    collection do
      get 'search'
    end
  end
  ```
  ```:onオプションを使う
  resources :photos do
    get 'search', on: :collection
  end
  ```
- 追加されたnewアクションへのルーティングを追加する
  - GET + /comments/new/previewのパスが認識される
  - Commentsコントローラのpreviewアクションにルーティング
  - preview_new_comment_urlやpreview_new_comment_pathが生成される
##### 参考資料
  - https://railsguides.jp/routing.html
  - https://programming-i.net/rails-routing
  - https://docs.google.com/document/d/1WcR-ucolWZXaQtHd7lsx06bK57-wuQzamM8md0COcHM/edit?usp=sharing
  - https://web-camp.io/magazine/archives/16815
