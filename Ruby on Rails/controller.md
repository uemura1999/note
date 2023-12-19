# コントローラの役割
- 概要
 - コントローラはモデルとビューの間を仲介する
  - コントローラがモデルのデータをビューで利用可能にすることで、データをビューで表示したり、入力されたデータでモデルを更新する
# コントローラの命名規約
- 規約
 - 基本的に英語の「複数形」を使う
 - 参考：モデルの命名規約は「単数形」が期待される

# メソッドとアクション
- 概要
 - アプリケーションがブラウザからのリクエストを受け取ると、ルーティングによってコントローラとアクションが確定し、Railsはそれに応じてコントローラのインスタンスを生成し、アクション名と同じ名前のメソッドを実行する
- 例：クライアントを一人追加する(1)
 1. ブラウザでappの/clients/newにアクセス
 2. ClientsControllerのインスタンスを作成しnewメソッドを呼び出す
 3. 指定がないとnewアクションがnew.html.erbビューをレンダリング
 ```
 class ClientsController<ApplicationController
  def new
  end
 end
 ```
- 例：クライアントを一人追加する(2)
 4. newアクション(メソッド)内でClientモデルを新規作成
 5. @clientインスタンス変数が作成
# パラメータ
- 概要
 - ユーザ側のアクションによってページ遷移の時に一緒に送られるデータ（リクエスト情報）
- ２種類のパラメータ
1. クエリ文字列パラメータ
 - URLの末尾に?から始まり、キーと値のペアがキー = 値の形で続く
 - http://example.com/page?name=John&age=25というURLでは、nameとageがクエリ文字パラメータであり、それぞれの値はJohnと25となる
- 例：/clirnts?status=activated
 ```
 def index
  // Client.activatedメソッドクライアントを取得し、@clientsに代入
  if params[:status] == "activated"
   @clients = Client.activated
   // それ以外は無効なクライアントを取得し代入する
  else
   @clients = Client.inactivated
  end
 end
 ```
2. POSTデータ
　- データの送信やリソースの作成などに使用され、情報はHTTPリクエストのボディ部分に格納される
- 例：新しいクライアント情報を作成し、dbに保存
 ```
 def create
  // POSTデータを使用し、新規Clientオブジェクトを作成
  @client = Client.new(params[:client])
  // dbに新しいレコードの保存が成功したら詳細ページにリダイレクト
  if @client.save
   redirect_to @client
  else
  // 保存に失敗したらnewビューを再表示するように上書き
  // ユーザーは再度データを入力して送信できるようになる
   render "new"
   end
  end
 end
 ```
## ハッシュと配列のパラメータ
- 概要
 - paramsハッシュには、一次元のキーバリューペアの他に、ネストした配列やハッシュも保存できる
- 例：値の配列をフォームから送信する方法
 - GET /clients?ids[]=1&ids[]=2&ids[]=3
  - キー名に空の角かっこ[]のペアを追加
  - 受け取ったparams[:ids]の値は["1", "2", "3"]
  - パラメータの値は常に「文字列」となる
- 例：フォームからハッシュを送信する方法
 - params[:client]の値
　　- {"name" => "Acme","phone" => "12345", "address" =>{ "postcode" => "12345", "city" => "Carrot City" }}
 - params[:client][:address]のハッシュがネストしている
 ```
 <form accept-charset="UTF-8" action="/clients" method="post">
  <input type="text" name="client[name]" value="Acme" />
  <input type="text" name="client[phone]" value="12345" />
  <input type="text" name="client[address][postcode]" value="12345" />
  <input type="text" name="client[address][city]" value="Carrot City" />
 </form>
 ```
## Jsonパラメータ
- 概要
 - リクエストの"Content-Type"ヘッダーが"application/json"に設定されていれば、JSON形式のパラメータを自動的にparamsハッシュで読み込んで通常と同じようにアクセス可能
- 例：JSON形式のデータをRailsアプリケーションで取り扱う方法
 1. JSON形式のコンテンツを送信
 ```
 { "company": { "name": "acme", "address": "123 Carrot Street" } }
 ```
 2. コントローラのparam[:company]が受け取る値
 ```
 { "name" => "acme", "address" => "123 Carrot Street" }
 ```
### 用語解説
 - JSON
  - テキストをベースにした軽量なデータ交換を行うフォーマット
  - [キー=値]のオブジェクトや、配列などを含むテキスト形式のデータ
 - Content-Typeヘッダー
  - 情報の種類や形式を表すもの(image, jpeg)
##### 参考資料
 - https://railsguides.jp/action_controller_overview.html
 - https://www.javadrive.jp/rails/controller/index6.html
 - https://zenn.dev/masahiro_toba/books/3daf7dc0d5dfd7/viewer/e4239c

# レンダリングの概要
- 概要
 - コントローラとビューの間でのやりとり
 - レスポンス内容を決定し、適切なメソッドを呼び出す
# レスポンスを作成する
- HTTPレスポンスの作成方法
 - renderを呼び出す
  - 完全なレスポンスを作成してブラウザに送信する
 - redirect_toを呼び出す
  - HTTPリダイレクトステータスコードをブラウザに送信する
 - headを呼び出す
  - HTTPヘッダーのみのレスポンスを作成してブラウザに送信する
## デフォルトのレンダリング（「設定より規約」）
- 概要
 - コントローラのアクションの末尾でレンダリングが指示されていない場合は、コントローラが利用可能なビューのパスから「アクション名.html.erb」ビューテンプレートを探し、それを使って自動的にレンダリングする」

## renderメソッドを使う
- 概要
 - appがブラウザで表示するコンテンツのレンダリングのほとんどは、コントローラのrenderメソッド
### Action Viewでレンダリングする
- 例：同じコントローラからデフォルト以外のテンプレートに対応するビューをレンダリングしたい場合
 ```
 def update
  @book = Book.find(params[:id])
  if @book.update(book_params)
    redirect_to(@book)
  else
  // updateメソッドの呼び出し失敗でedit.html.erbがレンダリングされる
    render "edit"
  end
 end
 ```
# requestオブジェクトとresponseオブジェクト
- ２つのアクセサメソッド(インスタンス変数の値を読み書きするメソッド)
 1. requestメソッド
  - リクエストを送ってきたユーザのヘッダー情報や環境変数を取得
 2. responseメソッド
  - クライアントに戻されようとしている内容を表す
## requestオブジェクト
- 概要
 - クライアントブラウザから返されるリクエスト
- request.××のプロパティ
| host | ホスト名を取得 |
| format | クライアントからリクエストされたContent-Typeヘッダーを取得 |
| method | HTTPメソッドを取得 |
| get? | HTTPメソッドがresources:のいずれかの場合にtrueを返す |
| headers | ヘッダー情報を取得 |
| port | リクエストで使われるポート番号（整数） |
| protocol | プロトコル名に"://"を加えたものを返す（"http://"など） |
| query_string | URLの一部で使われるクエリ文字 |
| remote_ip | クライアントのIPアドレス |
| url | リクエストで使われるURL全体 |
### patｈ_parameters, query_parameters, request_parameters
- 概要
 - リクエストに関連するすべてのパラメータをparamsハッシュに集約する
- requestオブジェクトの３つのアクセサメソッド
 1. query_parameters ハッシュ
  - クエリ文字列として送信されたパラメータが含まれる
  - /users?id=42の場合、{id:'42'} のような情報が含まれる
 2. request_parameters ハッシュ
  - POSTのbodyの一部として送信されたパラメータが含まれる
  - フォームから送信されたデータがあれば、ここに含まれる
 3. path_parameters ハッシュ
  - URLのパスに含まれる動的な部分がここに格納される
  - /users/42の場合、 {id:'42'} のような情報が含まれる
## responseオブジェクト
- 概要
 - クライアントに対して送り返すレスポンスに関する情報を保持
- responseのプロパティ
 - body
  - レスポンスの本文
  - HTMLで最もよく使われる文字列がここに格納される
 - status
  - レスポンスのステータスコード(404 file not foundなど)
  - クライアントに対してリクエストの成功・失敗を伝える
 - location
  - リダイレクトが行われた場合、そのリダイレクト先のURLがlocationに設定
  - リダイレクトがない場合は通常はnilとなる
 - content_type
  - レスポンスのContent-Typeヘッダーを表す
  - クライアントに対してどの種類のデータが返されているかを示す
 - charset
  - レスポンスで使われる文字セット（デフォルトは"utf-8"）
 - headers
  - レスポンスで使用されるヘッダーを表す
  - ヘッダーは様々な情報を含むため、headersハッシュを通じてアクセス
### カスタムヘッダーを設定する
- 概要
 - レスポンスでカスタムヘッダーを使いたい場合は、response.headersを利用
 - ヘッダに追加・変更を行う場合はresponse.headersに代入する
  - response.headers["Content-Type"] = "application/pdf"
