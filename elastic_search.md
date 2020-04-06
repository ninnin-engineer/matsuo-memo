# Elastic Searchに関するメモ


## ElastciSearchの特徴
RDBではない。以下のような特徴をもつ。
* 検索のパフォーマンスとスケーラビリティが強い
* 時間間隔でインデックスを区切ったりすることがよくある
* 不要になったインデックスは捨てる
* 分析や検索用のデータを保管する


## RDBの特徴
* データを安全に保管し、汎用的に利用するための機能が豊富
* データベースをドロップすることはなかなかない
* マスターとなるデータを半永久的に保管する


## RDBとの用語の違い
| RDB          | ElastciSearch    |
| ------------ | ---------------- |
| データベース | インデックス     |
| テーブル     | マッピングタイプ |
| カラム       | フィールド       |
| レコード     | ドキュメント     |


## 全文検索園児を使う考え方
* 全文検索エンジンの中に検索対象のデータが入っている
* アプリケーション側で検索すると、検索エンジンにクエリが投げられ、結果が返ってくる
* アプリケーション側で検索対象のデータが更新されると、ちゃんと検索エンジンの中のデータにも更新が反映される


## 使い方に関する情報
* サーチAPIを利用して検索
	- 検索やデータの投入、削除はJSON形式で指定することができる。
* 本番環境の検索はエイリアス名を使う
	- 複数のインデックスに同じ名前のエイリアスの定義可能
	- 一つのエイリアスを指定するだけで複数のインデックスを検索できる。
* ページング
	- size: 一度の検索結果を取得する数(デフォルトは10)
	- from: スキップする結果数を指定(デフォルトは0)


## railsのgem
```
gem 'elasticsearch-model'
gem 'elasticsearch-rails'
```

### elasticsearch-model
```
include ElasticSearch::Model
```
をモデルに追加することでそのモデルが検索対象になる。  
```
Article.__elasticsearch__.create_index! force: true
```
のコードでElasticSearchにインデックスが作れるが、実際はこれを書かなくてもincludeするだけで自動で  
インデックスを作成している。

### elasticsearch-rails
Elasticsearchを使うためのrakeタスクやloggerのカスタマイズ、templateの提供などができるようです。  
RailsのModelをElasticSearchでも使えるようにするためのgem。これと`elasticsearch-model`はセットのようなもの。


## config設定
`config/initializers/elasticsearch.rb`に以下のように記述
```
# 「es」はdocker-composeのservicesに設定した名前
config = {
    host:  ENV['ELASTICSEARCH_HOST'] || "es:9200/",
}

Elasticsearch::Model.client = Elasticsearch::Client.new(config)
```


## ElasticSearchにドキュメントを入れる
以下のようにElasticSearchのモデルがある場合、
```
class Article < ActiveRecord::Base
  include Elasticsearch::Model
end
```
下記のように記述することによってドキュメントを入れることができる。
```
Article.import
```


## ElasticSearchにクエリを投げレスポンスを得る
基本としては以下のようにElasticSearchにクエリを送る。
```
response = Article.search 'fox dogs'
```
Rails側からElasticSearchにクエリを送る場合、クエリパラメータにparamsを使えばいい
```
def index
　　@articles = Article.search(params)
end
```
取得したデータは以下のように得られる。
```
response.took
# => 3

response.results.total
# => 2

response.results.first._score
# => 0.02250402

response.results.first._source.title
# => "Quick brown fox"
```


## ElasticSearchのデータの更新
手動でやる場合は以下のようにする。
```
Article.first.__elasticsearch__.update_document
```
しかし、以下のようにincludeしておけばレコードを更新すると同時にElasticSearchの  
documentも自動で更新してくれる。
```
class Article
  include Elasticsearch::Model
  include Elasticsearch::Model::Callbacks
end
```
