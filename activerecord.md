# Active Record メモ


## ActiveRecordを一言でいうと
RubyとSQLの翻訳機。  
RailsでModelのcreateだとかfindメソッドでDBがいじれるのは  
このActiveRecordを使ってるおかげ。


## ApplicationRecord
ActiveRecordを使ったApplicationRecordを継承することによってModelは  
ActiveRecordの機能を用いている。

app/models/application_record.rb
```
class ApplicationRecord < ActiveRecord::Base #ここでApplicationRecordにActiveRecordを継承
  self.abstract_class = true
end
```

app/models/blog.rb
```
class Blog < ApplicationRecord #BlogモデルにそのApplicationRecordを継承
end
```


## ActiveRecordの特徴
* どのDBを使用してもRubyで統一的にかける。


## オブジェクト/リレーショナルマッピング(O/Rマッピング, ORM)
オブジェクト(Model)をリレーショナルデータベースのテーブルに接続すること。


## ActiveRecord::Relation
O/Rマッパーといい、  
* クエリを生成するための条件を持っておいて、必要に応じて適切なSQLクエリを生成・発行してくれる
* その結果からオブジェクトを作って返したり保持したりしてくれる

`User.all`や`User.where(name: "hoge")`などで返ってくるものがActiveRecord::Relationのインスタンスです  
has_many関連で作られたメソッドはActiveRecord::CollectionProxyのインスタンスを返しますが、  
この`ActiveRecord::CollectionProxy`は`ActiveRecord::Relation`を継承しています。


## modelのscope
複数のクエリをまとめたActiveRecordを用いたModelのメソッド。  

scopeを使わない場合
```
class UsersController < ApplicationController
  ...
  def index
    @users = User.where(deleted: false).order(created_at: :desc)
  end
  ...
end
```

scopeを使った場合
```
class User < ApplicationRecrd
  ...
    # deletedカラムがfalseであるものを取得する
    scope :active, -> { where(deleted: false) }
    # created_atカラムを降順で取得する
    scope :sorted, -> { order(created_at: :desc) }
    # activeとsortedを合わせたもの
    scope :recent, -> { active.sorted }
  ...
end
```

以上のようにすることによって、以下のように再利用が可能になる。
```
class UsersController < ApplicationController
  ...
  def index
    @users = User.recent
  end
  ...
end
```


## QueryObjectパターン

### QueryObjectとは
それ自身が、SQLクエリーとして機能するオブジェクト構造。

### どんなときにQueryObjectパターンの使うか
`ActiveRecord`リレーションで実行しなければならないクエリが複雑になった時。  
目安として、スコープが複数のカラムとやり取りする場合や、他のテーブルとJOINする場合は、Query Objectへの移行を検討すべきです。

### scopeからQueryObject
以下のscopeを使ったコードは
```
class Video < ActiveRecord::Base
  scope :featured_and_popular,
        -> { where(featured: true).where('views_count > ?', 100) }
end
```
以下のQueryObjectを用いた記述で書ける。
```
module Videos
  class FeaturedAndPopularQuery
    def initialize(relation = Video.all)
      @relation = relation
    end

    def featured_and_popular
      @relation.where(featured: true).where('views_count > ?', 100)
    end
  end
end
```
このQueryObjecth以下のようにして呼ぶことができる。
```
Videos::FeaturedAndPopularQuery.new.featured_and_popular
```


## BULK INSERT
テーブルに複数のレコードを登録する時に、  
1行1行INSERTすると効率が悪いので、まとめてINSERTすること。  
[詳細はこのページを参照](https://qiita.com/xhnagata/items/79184ded56158ea1b97a)

### 必要なGem
```
gem 'activerecord-import'
```

### SQLでの説明
```
INSERT INTO books (columns) VALUES (values);
INSERT INTO books (columns) VALUES (values);
INSERT INTO books (columns) VALUES (values);
```
以上が以下のようになる
```
INSERT INTO books (columns) VALUES (values), (values), (values);
```

### RailsでのBULK INSERT(import)
```
books = []
10.times do |i|
  books << Book.new(:name => "book #{i}")
end
Book.import books
```

### メリット
毎回毎回コミットを行わないので、こっちの方が早くなる。
