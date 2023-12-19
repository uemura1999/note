# Action Controllerの概要
## コントローラの役割
- 概要
  - コントローラはモデルとビューの間を仲介する
  - リクエストを処理するコントローラがルーティング設定によって決定されると、コントローラはリクエストの意味を理解して適切な出力を行う役目を担う
  - コントローラがモデルのデータをビューで利用可能にすることで、データをビューで表示したり、入力されたデータでモデルを更新
## コントローラの命名規約
- 概要
  - 規約を守ることで、デフォルトのルーティングジェネレータをそのまま利用できる
- 規約
  - 基本的に英語の「複数形」を使う
  - 絶対に守るものではない(例：ApplicationController)
  - 参考：モデルの命名規約は「単数形」が期待される

## メソッドとアクション
- 概要
  - コントローラは、ApplicationControllerを継承しており、他のクラスと同様のメソッドが使える
  - アプリケーションがブラウザからのリクエストを受け取ると、ルーティングによってコントローラとアクションが確定し、Railsはそれに応じてコントローラのインスタンスを生成し、アクション名と同じ名前のメソッドを実行
- 例：クライアントを一人追加する
```
class ClientsController<ApplicationController
  def new
  end
end
```
  - 1.ブラウザでappの/clients/newにアクセス
  - 2.RailsはClientsControllerのインスタンスを作成しnewメソッドを呼び出す
  - 3.Railsでは指定のない場合、newアクションがnew.html.erbビューをレンダリングする
```
def new
  @client = Client.new
end
```
  - 4.newアクション(メソッド)内でClientモデルを新規作成
  - 5.@clientインスタンス変数が作成
  - 6.@clientインスタンス変数は、ビューでもアクセス可能
## パラメータ
- 概要
  - ユーザ側のアクションによってページ遷移の時に一緒に送られるデータ（リクエスト情報）
  - Railsに限らず、Webアプリケーションでは2種類のパラメータを扱う
- クエリ文字列パラメータ（クエリパラメータ）
  - URLの末尾に?から始まり、キーと値のペアがキー = 値の形で続く
  - http://example.com/page?name=John&age=25というURLでは、nameとageがクエリ文字パラメータであり、それぞれの値はJohnと25となる
- 例：表示内容をフィルタリング
```
def index
 if params[:status] == "activated"
  @clients = Client.activated
 else
  @clients = Client.inactivated
 end
end
```
  - 0.params[:state]
    - リクエストから取得されたstatusというクエリ文字パラメータの値
  - 1.もしparams[:status]が"activated"であれば、Client.activatedというメソッドを使用して有効なクライアントを取得し、@clientsに代入
  - 2.それ以外の場合（"activated"でない、またはstatusが指定されていない場合）、Client.inactivatedというメソッドを使用して無効なクライアントを取得し、@clientsに代入
- POSTデータ
  - データの送信やリソースの作成などに使用され、情報はHTTPリクエストのボディ部分に格納される
- 例：新しいクライアント情報を作成し、dbに保存
```
def create
 @client = Client.new(params[:client])
 if @client.save
  redirect_to @client
 else
  render "new"
  end
 end
end
```
  - @client = Client.new(params[:client]):
    - リクエストから送信されたデータ（POSTデータ）を使用して、新しいClientオブジェクトを作成
    - （params[:client]は、クライアントから送信されたデータの部分であり、通常はフォームの入力フィールドからのデータが含まれる）
  - if @client.save:
    - クライアント情報がバリデーションをクリア
    - データベースに正常に保存された場合は、指定されたクライアントの詳細ページにリダイレクト
  - else:
    - もしデータの保存に問題がある場合、新しいクライアントを保存せずに、newアクションに戻る
    - ただし、この行によりnewビューを直接表示せずに、newビューをレンダリングすることが示される
  - render "new":
    - データの保存に問題があった場合、通常は"create"ビューが表示されるが、この行によりnewビューが代わりに表示される
    - エラー発生時に元のフォームを維持し、エラーメッセージを表示

### ハッシュと配列のパラメータ
- 概要
  - paramsハッシュには、一次元のキーバリューペアの他に、ネストした配列やハッシュも保存できる
- 例：値の配列をフォームから送信する方法
```
GET /clients?ids[]=1&ids[]=2&ids[]=3
```
  - キー名に空の角かっこ[]のペアを追加
  - 受け取ったparams[:ids]の値は["1", "2", "3"]
  - パラメータの値は常に「文字列」となる
- 例：フォームからハッシュを送信する方法
```
<form accept-charset="UTF-8" action="/clients" method="post">
  <input type="text" name="client[name]" value="Acme" />
  <input type="text" name="client[phone]" value="12345" />
  <input type="text" name="client[address][postcode]" value="12345" />
  <input type="text" name="client[address][city]" value="Carrot City" />
</form>
```
  - params[:client]の値
    - {"name" => "Acme","phone" => "12345", "address" =>{ "postcode" => "12345", "city" => "Carrot City" }}
  - params[:client][:address]のハッシュがネストしている
  - paramsハッシュはハッシュのように振る舞いますが、キー名にシンボルと文字列のどちらでも指定できる

### Jsonパラメータ
- 概要
　- リクエストの"Content-Type"ヘッダーが"application/json"に設定されていれば、JSON形式のパラメータを自動的にparamsハッシュで読み込んで通常と同じようにアクセス可能です
- JSON形式のデータをRailsアプリケーションで取り扱う方法
  - 1.JSON形式のデータ
  { "company": { "name": "acme", "address": "123 Carrot Street" } }
    - companyというキーの値は、nameとadressキーを持つ別のオブジェクトである
  - 2.RailsでのJSONパラメータの取得
  params[:company] # => { "name" => "acme", "address" => "123 Carrot Street" }
    - Content-Typeヘッダーがapplication/jsonに設定されていることが前提
    - RailsはJSONデータをparamsハッシュに変換して取得できる
  - 3.wrap_parameters:
    - Railsのデフォルトではコントローラ名に応じたキーでラップされる
    - このラップを無効にしたい場合に、wrap_parametersを使用する
    - データの送信先がCopaniesControllerであれば、params[:comapny]を使ってアクセスできるようラップされる
  - 4.JSONをRubyに変更
   JSON：{ "name": "acme", "address": "123 Carrot Street" }
   Ruby：params[:company] # => { "name" => "acme", "address" => "123 Carrot Street" }
- 用語解説
  - JSON
    - テキストをベースにした軽量なデータ交換を行うフォーマット
    - キーと値のペアで構成されるオブジェクトや、値のリストを表現する配列などを含むテキスト形式のデータ
  - Content-Typeヘッダー
    情報の種類や形式を表すもの(image, jpeg)