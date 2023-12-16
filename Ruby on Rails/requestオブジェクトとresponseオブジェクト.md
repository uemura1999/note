# requestオブジェクトとresponseオブジェクト
- ２つのアクセサメソッド(インスタンス変数の値を読み書きするメソッド)
　- requestメソッド
　　- リクエストを送ってきたユーザのヘッダー情報や環境変数を取得
　- responseメソッド
　　- クライアントに戻されようとしている内容を表す
## 10.1 requestオブジェクト
- 概要
　- クライアントブラウザから返されるリクエスト
- requestのプロパティ
| host | リクエストで使われるホスト名 |
| domain(n=2) | ホスト名の右から数えてn番目のセグメント |
| format | クライアントからリクエストされたContent-Typeヘッダー |
| method | リクエストで使われるHTTPメソッド |
| get? | HTTPメソッドがresources:のいずれかの場合にtrueを返す |
| headers | リクエストに関連付けられたヘッダーを含むハッシュを返す |
| port | リクエストで使われるポート番号（整数） |
| protocol | プロトコル名に"://"を加えたものを返す（"http://"など） |
| query_string | URLの一部で使われるクエリ文字（"?"より後の部分） |
| remote_ip | クライアントのIPアドレス |
| url | リクエストで使われるURL全体 |
### 10.1.1 patｈ_parameters, query_parameters, request_parameters
- 概要
　- リクエストに関連するすべてのパラメータをparamsハッシュに集約
- requestオブジェクトの３つのアクセサメソッド
　1.query_parameters ハッシュ
　 - クエリ文字列として送信されたパラメータ
　2.request_parameters ハッシュ
　 - POSTのbodyの一部として送信されたパラメータ
　3.patｈ_parameters ハッシュ
　 -  ルーティング機構によって特定のコントローラとアクションへのパスの一部であると認識されたパラメータ
## 10.2 responseオブジェクト
- 概要
　- 直接使うことはないが、「after系」フィルタ内などでresponseオブジェクトを直接操作できると便利
　- responseオブジェクトのアクセサメソッドにセッターがあれば、それを利用してresponseオブジェクトの値を直接変更できる
- responseのプロパティ
| body | 	クライアントに送り返されるデータの文字列 |
| status | レスポンスのステータスコード(404 file not foundなど) |
| location| リダイレクト先URL（存在する場合） |
| content_type | レスポンスのContent-Type ヘッダー |
| charset | レスポンスで使われる文字セット（デフォルトは"utf-8"） |
| headers | レスポンスで使われるヘッダー |
### 10.2.1 カスタムヘッダーを設定する
- 概要
　- レスポンスでカスタムヘッダーを使いたい場合は、response.headersを利用できる
　- このヘッダー属性はハッシュであり、ヘッダ名と値がその中でマップされている
　- 一部の値はRailsによって自動的に設定される
　- ヘッダに追加・変更を行いたい場合は以下のようにresponse.headersに代入する
　response.headers["Content-Type"] = "application/pdf"
