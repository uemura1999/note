# RESTful設計とは
## REST
- 設計思想と設計、実装の関係性
　- 設計思想が大元にあって、その設計思想を実現できるよう大枠を具体的に規定するのが設計で、さらにその設計を具体的に実現したのが実装
　- 実装を一段階抽象化したのが設計で、設計をさらに一段階抽象化したのが設計思想
　- お互いのレイヤーで口出ししない
　　- 設計はシステムの構成を定めるが、それをどう実装するかは口出ししない
### RESTの原則
- クライアントとサーバーの分離（Client-Server：CSスタイル）
　- app全体をクライアントコンポーネントとサーバーコンポーネントに分離
　- 通常、UIの機能を全てクライアントに集約する形で行われる
　


## RESTful
- REST の設計思想を体現したアーキテクチャ



- 参照URL
　- https://zenn.dev/wsuzume/articles/280ba652dc4fc7










# 参考
- Webのしくみ
　- ブラウザからWebサーバへリクエスト(URL,リクエストメソッド)を送る
　- レスポンス(ステータスコード)(HTML,CSS,JS...)を返す
- リクエストメソッド
　- GET(取得)，POST(作成), PUT(更新), DELETE(削除)の４つだけ
　- Create, Read, Update, Deleteの頭文字からCRUDとも呼ぶ
- リクエスト
　- メソッド「どうする」＋URL「モノ」
　- GET http:/example.com/users/show/123
- ステータスコード
　- リクエストに対するWebサーバーからの結果を伝える
　- 成功・失敗とその理由
　- ３桁の数字（＋対応する説明）
　| 200 | 成功 |
　| 300 | リダイレクト(URLが変わった) |
　| 301 | Moved Permanently |
　| 400 | クライアントが原因のエラー |
　| 404 | Not Found |
　| 500 | サーバが原因のエラー(Internal Server Error) |
- 参照URL
　- https://www.slideshare.net/tkawa1/learning-rest-from-rails-style


