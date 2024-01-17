docker-compose run --rm web bundle exec rails g model Doctor answer:integer
- docker-compose run --rm webここまでが一区切り
- web-containerとdb-containerを協調して使いたい場合
  - docker-compose
- web:docker-compose.yml
  - web:の内容を定義している
  - make webで簡単に起動してくれる
- コマンドを忘れるのでMakefileを確認（VSコードの中でcommand+p）
- tab
  - 候補を出して自動で入力してくれる