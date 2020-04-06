# 殴り書きメモ

## Rubyの"::"
Class.methodみたいな奴の、methodだけじゃなく定数も指定できるようになったもの。  
Rubyではクラスや変数等も定数として扱われる。

## ack
`ack 文字列`でその文字列がどのファイルにあるかが検索できる。

## jbuilderとは
html.erbではなく、json形式でViewを書くためのテンプレ

## コンテナ内に入る
`docker-compose exec web bash`

## 予約とかのデータが表示されない場合
`bundle exec rails elasticsearch:recreate_reservation`

## pry+routes+grep
`show-routes -G [string]`



## slim

### 参考サイト
https://qiita.com/pugiemonn/items/b64171952d958dc4d6be

### <% %>
`-`で書く。
```
- if logged_in?
 li = link_to 'イベントを作る', new_event_path
 li = link_to 'ログアウト', logout_path
- else
 li = link_to 'Twitterでログイン', '/auth/twitter'
```

### <%= %>
`=`
```
= form_for @event, :html => { class: "ui form"} do |f|
  = f.text_field :title, placeholder: "タイトル"
  = f.submit "イベントを作成", class: "btn"
```

### コメント
`/`

### aタグ
`a href="//example.com" target="_blank" hoge`

### imgタグ
`img src="https://pbs.twimg.com/profile_images/1739640742/20120108024459Comic20120102_001-1_normal.jpg" alt="ぷぎえもん"`

### pタグ
`p ほげほげ　ふがふが`
は以下になる。
```
<p>
      ほげほげ　ふがふが
</p>
```

### 改行
`|`パイプを使う。  
```
p
  |
    ほげほげ　ふがふが
    ほげほげ　ふがふが
```  
は以下になる。
```
<p>
      ほげほげ　ふがふが
      ほげほげ　ふがふが
</p>
```

### Ruby変数を使用
```
h1 = @event.title
```  
※パイプを使うとうまく行かない

### IDの指定(div)
`#pam`  
と書くと、以下のようにidが表示される。尚、エレメントを指定しない場合は自動で`div`になる。
```
<div id="pam">
</div>
```

### Classの指定(div)
`.piyo.hoge`  
は以下のようになる。
```
<div class="piyo hoge">
</div>
```

### ネスト
```
#pam
  .hoge
    p.huga.piyo
      |
        わーいわーい
```
は以下のようになる。
```
<div id="pam">
  <div class="hoge">
    <p class="huga piyo">
      わーいわーい
    </p>
  </div>
</div>
```

### インラインHTML
```
.ui.grid
　　- @quiz.each do |q|
　　　　<div id="quiz_#{q.id}">クイズ#{q.id}</div>
```
は以下のようになる。  
```
<div class="ui grid">
　　<div id="quiz_1">クイズ1</div>
　　<div id="quiz_2">クイズ2</div>
</div>
```


## ブラウザ遷移の無いWebアプリの解析
Reactを使用すると、viewにてslimでReactコンポーネントを呼び出し、そのReactがJSX記法等の  
フロントエンド側の謎の技術によってレンダリングする。その際、ブラウザ遷移が起きないため、何が呼ばれているのかが  
わかりずらい。そういう時は、「開発者ツールのネットワーク」を使用すると便利になる。


## Reduxとは
React.jsが扱うUIのstate(状態)を管理するためのフレームワークです。React.jsで複雑なアプリケーションを  
作るとstateの変更箇所が複数に分散(componetが複数箇所で保持)して、管理が困難になるのを解決するためです。


## トランザクション
```
ActiveRecord::Base.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end
```
これの意味は、二つの処理中一つが失敗するとコードブロックにあるSQL処理  
を全部ロールバックすることができる。


## AASM (Ruby State Machine)
Rubyのclassに有限オートマトンを追加するライブラリ。オブジェクトの状態遷移管理は大事。それを簡単に実装できるgemがAASM。  
stateが状態で、event(イベント)の中にtransition(遷移)を定義する。
```
class Job
  include AASM

  aasm do
    state :sleeping, :initial => true
    state :running
    state :cleaning

    event :run do
      transitions :from => :sleeping, :to => :running
    end

    event :clean do
      transitions :from => :running, :to => :cleaning
    end

    event :sleep do
      transitions :from => [:running, :cleaning], :to => :sleeping
    end
  end

end
```


## Reform
Trailblazerに含まれるgem。Form Object用のGemである。わかりやすいデータ構造でhtmlのformを作成することができる。  
保存時のパラメータの検証・Modelへの受け渡しを容易にする。


## OpenStruct
要素を動的に追加・削除できる手軽な構造体を提供するクラス。
```
require "ostruct"

config = OpenStruct.new
config.level #=> nil
config.level = :debug
config.level #=> :debug
```


## POST
GETはHTTPヘッダだけでBodyは空だが、POSTはBodyを入れて送信する。


## I18n
Internationalization の略。

### tメソッド
```
<%= t("fruits.apple") %>
<%= t("fruits.orange") %>
```
の時、以下のようにロケールの設定がja(日本語)であればja.ymlを、en(英語)であればen.ymlを読み込みます。
```
ja:
  fruits:
    apple: "りんご"
    orange: "みかん"
```
```
en:
  fruits:
    apple: "Apple"
    orange: "Orange"
```

### lメソッド
lメソッドは日付や時刻を表す際に用います。  
国や地域によって時刻は異なりますが、lメソッドを使えば現地時間に対応できます。
```
<%= l(Time.current) %>
```
は以下のようになる。
```
ja:
  time:
    current: "%Y年%m月%d日"
```
```
en:
  time:
    current: "%m %d, %Y"
```


## routes
"http://localhost:3000/rails/info/routes"でルーてイング情報が見れる。  
`rails routes`みたいに待たなくていいから楽。


## 同期処理/非同期処理

### 同期処理
書いた順番に実行されていく。重たい処理が間にあると、そこで大きな待ち時間が生まれる。

### 非同期処理
外部のサーバと通信する関数を呼んだあと、レスポンスが返るまでに一旦関数から抜けて別の処理を進めて レスポンスを受け取り次第、呼び出し元に値を返す。  
Aという関数が完了次第、実行したい関数(function)を引数として渡して実行させるものをコールバック関数という。  


## NOT NULL制約
NULLを格納する許可を与えるかどうかを設定します。デフォルトではNULLが許可されています。NULLを許可しない場合には「:null => false」をオプションに指定します。

## DEFAULT制約
デフォルト値の設定をする場合は「:default => 値」をオプションに指定します。


## Sidekiq
Rails アプリで非同期処理を行うためのライブラリです。複数のジョブを同時に実行することができ、メモリを節約することができます。  
非同期処理を行うクラスは Sidekiq::Worker を include してつくります。あとは perform メソッドの中に非同期で行いたい処理を書くだけです。

## FactoryBot
FactoryBotとは、テストデータの作成を手伝ってくれるGem。  
userというテストデータを用意する時、
```
FactoryBot.define do
  factory :user do
    name { 'Yamada Tarou' }
    email { 'yamade@rails.com' }
    password { 'password' }
    password_confirmation { 'password' }
  end
end
```
この定義をテストで利用すると、  
```
RSpec.describe UsersController, type: :controller do
  let(:user){ FactoryBot.create(:user) }
end
```
このように書くことができる。


## rspecのparamsの値の決め方
rspecのrequestでparamsを送る時、どんなparamsを入れたら良いかというのはフォームにある記載事項で決まり、  
そのフォームに必要な項目は、usecaseとかの中のreformでformをnewする際に、  
`@form = ::HotelArrangements::HotelArrangementRefunds::CreateForm.new()`  
のように書いてあったら、`create_form.rb`にフォームに必要なパラメータが書いてある。


## rubyのcase文
```
case 対象オブジェクト
when 値1 then
  値1と一致する場合に行う処理
when 値2 then
  値2と一致する場合に行う処理
when 値3 then
  値3と一致する場合に行う処理
else
  どの値にも一致しない場合に行う処理
end
```


## pryでURLのコントローラやアクション調べる
```
recognize-path /path
```

## postman
ログイン認証とかがついてるものだと、Headerに認証トークンが必要な場合が多々ある。  
その場合、以下のようにHeaderを設定する。(Bearer認証方式の場合)
```
Key: Value
Authorizaton: Bearer ***
```
何か足りなかったりするとHTMLがそのままかえってきたりする。


## include文
モジュールの性質を他のクラスに追加するために使用されるメソッド。
```
module Sayhello
  def hello
    p 'hello'
  end
end

class Sayhello_2
  include Sayhello
end

a = Sayhello_2.new
a.hello
```

## モジュールのMixin機能
モジュールは厳密には継承ではないのですが、Mixinという技術を用いて多重継承を可能としています。
```
module Say
  def say
    p "say"
  end 
end
 
module Hello
  def hello
    p 'hello'
  end  
end
 
class Sayhello
  include Say
  include Hello
end
 
a = Sayhello.new
a.say
a.hello
```
また、モジュールはインスタンスは立てられない。


## require文
requireは外部ファイルやライブラリを読み込むのに使う。


## シンボル
```
str = "名前"
sym = :name

p str.class -> String
p sym.class -> Symbol
```
ハッシュとかだと文字列をキーにする以下のような方法と、
```
baby = {"name" => "Rick Ross", "age" => 1, "origin" => "Compton"}
```
シンボルをキーに取る以下のような方法がある。
```
boy = {name: "Kendrick Lamar", age: 3, origin: "Miami"}
```
省メモリで可読性が高い。こっちの方が早いと言われている。


## before_action
railsではcontrollerでbefore_actionを定義することで、アクションの前に処理を差し込むことができます。  
一般的に、複数のアクションで共通して必要になる処理などをbefore_actionで定義することが多い。  
例えば、ログイン機能があるやつで各アクションでユーザーがログインしているかのチェックを行うようなときに、  
ログイン認証の処理をbefore_actionとして定義することが多い。
```
class SampleController < ApplicationController
  before_action :メソッド名
  ・・・
  def メソッド名
  ・・・
  end
end
```

## Rubocop
RuboCopを使うことで、ソースコードに対して以下のようなチェックをおこないます。
* インデント
* 文字数の長さ
* メソッド内の行数
* 条件式の見やすさ
* ソースコードの複雑度
* ハッシュなどの末尾にあるカンマの有無
使い方は、rubocopコマンドを実行して、ソースコードがRuboCopの規約（ルール）に沿っているかをチェックするだけです。


## delegate
```
class Aisatsu < ActiveRecord::Base
  def hello
    'hello'
  end

  def goodbye
    'goodbye'
  end
end

class Taro < ActiveRecord::Base
  has_one :aisatsu
  delegate :hello, to: :Aisatsu
end
```
本来なら、taro.aisatsu.helloとしなければならないが、`delegate :hello, to: :Aisatsu`として  
あげると`hello = (aisatsu.hello)`相当として使用できるようになり、`taro.hello`とできる。


## request
```
request.path
  # /book/231/reg/top
-----
request.fullpath
  # /book/231/reg/top?uid=d4f4efb0-a2dc&type=2
request.original_fullpath
  # /book/231/reg/top?uid=d4f4efb0-a2dc&type=2
-----
request.url
  # https://test.jp/book/231/reg/top?uid=d4f4efb0-a2dc&type=2
  => protocolがhttpまたはhttpsの2択
request.original_url
  # https://test.jp/book/231/reg/top?uid=d4f4efb0-a2dc&type=2
  => base_urlを使っており、その中でschemaメソッド内を使ってget_headerメソッドでプロトコルを取得しているので、http/https以外でもOK。
```


## enum
rubyではなくRailsの方の機能。  
モデルに定義することができる型で、データベースの特定のカラムの値（整数値）を、  
指定した文字列（シンボル）で扱うことができるようになります。
```
class User < ApplicationRecord
  enum status:[:guest, :active, :vip, :freeze]
end
```
先頭のシンボルから順番に値が割り当てられ（:guestが0、:activeが1、:vipが2、:freezeが3）  
次のように、プログラムコードでこれらのシンボルを利用することができるのです。
```
  :
def check_user(status)
  if status == :freeze
    # ...凍結会員の場合
  else
    # ...生きている会員の場合
  end
end
  :
```

## unless

### 普通のunless文
ただの否定版条件分岐
```
# モデルが存在することを否定
if モデル.!present?

# モデルが存在しない
unless モデル.present?
```

### 後置unless文
```
x = 10
y = 0
puts x / y unless y == 0
```
この場合、unless以降の条件式が成立しない時しか左の処理を実行しない。


## persisted
DB に保存済みかどうかを判定するメソッド。
```
user = User.first
user.persisted?
#=> true
new_user = User.new
user.persisted?
#=> false
```


## attr_reader
これをつけると、`@`マークをつける必要がなくなる。


## authorize_resource
adminUserが会社の人で、adminUser一人一人に役職(role)が割り振られている。例えばエンジニアの人が  
実際に人とかの予約が見れたらまずい。
```
authorize_resource :hotel_arrangement
```
のように書くことによって、`hotel_arrangement`のモデルをいじる権限があるのかどうかをそのコントローラを使用する前に  
確認する。


## reload.decorate
```
def create
	form = ::HotelArrangements::AddRefundUsecase.new(@hotel_arrangement, params).execute

	if form.errors.empty?
		@hotel_arrangement = @hotel_arrangement.reload.decorate
		render "api/manager/hotel_arrangements/show", status: :created
	else
		render json: { errors: form.errors.full_messages.join('\n') }, status: :unprocessable_entity
	end
end
```
親モデル(hotel_arrangement)と子モデル(hotel_arrangement_refund)があり、`execute`で子モデルのデータを  
作成しているが、親モデルからすると、子モデルが追加されたこと自体に気づいてない場合があるので、  
`reload.decorate`によって親のほうを更新することによってエラーを防ぐ。  

```
render "api/manager/hotel_arrangements/show", status: :created
```
ここでは`tabi/app/views/api/manager/hotel_arrangements/`このディレクトリの中のファイルである、  
`show.json.jbuilder`を指している。このファイルの中身は以下のようになっている。
```
json.partial! partial: 'api/manager/hotel_arrangements/hotel_arrangement', locals: { hotel_arrangement: @hotel_arrangement }
```
これは、同じディレクトリ下にある`_hotel_arrangement.json.jbuilder`というファイルを指すような記述であり、  
このファイルの中には以下のように書いてある。`json.merge! hotel_arrangement.to_json`  
ここで`to_json`が使われているが、これは実は普通のrailsの`to_json`ではなく、上の行の`reload.decorate`の`decorate`  
の部分でオーバーライドされている。そのdecorateのファイルは、`/tabi/app/decorators/hotel_arrangement_decorator.rb`  
にある。このファイルの中身には以下のように`to_json`がオーバーライドされている。
```
def to_json(*_args)
  attributes
    .except(:updated_at, :created_at)
    .merge(
      hotel_name: hotel.name,
      total_price: total_price&.format,
      total_price_cents: total_price&.cents,
      total_price_currency: total_price&.currency&.iso_code,
      price: price&.format,
      price_cents: price&.cents,
      price_currency: price&.currency&.iso_code,
      billing_price: billing_price&.format,
      billing_price_cents: billing_price&.cents,
      billing_price_currency: billing_price&.currency&.iso_code,
      arrange_fee: arrange_fee&.format,
      arrange_fee_cents: arrange_fee&.cents,
      arrange_fee_currency: arrange_fee&.currency&.iso_code,
      confirmation_deadline_at: confirmation_deadline_at,
      total_refunded_amount: total_refunded_amount&.format,
      total_refunded_amount_cents: total_refunded_amount&.cents,
      total_refunded_amount_currency: total_refunded_amount&.currency&.iso_code,
      total_refund_fee: total_refund_fee&.format,
      total_refund_fee_cents: total_refund_fee&.cents,
      total_refund_fee_currency: total_refund_fee&.currency&.iso_code,
      hotel_arrangement_plans: hotel_arrangement_plans.map(&:to_json),
      hotel_arrangement_payments: hotel_arrangement_payments.map(&:to_json),
      hotel_arrangement_refunds: hotel_arrangement_refunds.map(&:to_json),
      reservation_id: external_service_reservation_id,
      auto: auto?,
      permitted_statuses: permitted_statuses,
      can_send_eticket: sendable_eticket?
    )
end
```
ここで、以下の文からわかる通り、  
```
hotel_arrangement_payments: hotel_arrangement_payments.map(&:to_json),
```
この`to_json`メソッドでは、refundの時は違うファイルの方を使うように指示されており、  
`hotel_arrangement_refund_decorator.rb`の以下の部分の`to_json`がオーバーライドされている。
```
def to_json(*_args)
  attributes
    .merge(
      refunded_amount: refunded_amount.format,
      refund_fee: refund_fee.format,
      cancellation_penalty: cancellation_penalty.format,
    )
end
```
つまり、decorateではViewで使うためのメソッドを切り離した物が書いてあり、この行で  
それ以降の行のメソッドがオーバーライドされている。


## resourcesのonly
```
resources :hotel_arrangements, only: [] do
  resources :hotel_arrangement_booking_coms, only: [:create]
end
```
このように書くと、resourcesで色々なアクションが生成される中、`create`だけが生成される。


## Vimのswpファイル
Vimが強制終了した時ように復元するために、編集時に勝手に作られ編集終了後に消える。  
gitにプッシュしても、バイナリだから表示されないためあんま気にしたくていい。


## mergeメソッド
rubyのHash同士を結合する関数。
```
h1 = { "a" => 100, "b" => 200 }
h2 = { "b" => 246, "c" => 300 }
h3 = { "b" => 357, "d" => 400 }
h1.merge          #=> {"a"=>100, "b"=>200}
h1.merge(h2)      #=> {"a"=>100, "b"=>246, "c"=>300}
h1.merge(h2, h3)  #=> {"a"=>100, "b"=>357, "c"=>300, "d"=>400}
h1.merge(h2) {|key, oldval, newval| newval - oldval}
                  #=> {"a"=>100, "b"=>46,  "c"=>300}
h1.merge(h2, h3) {|key, oldval, newval| newval - oldval}
                  #=> {"a"=>100, "b"=>311, "c"=>300, "d"=>400}
h1                #=> {"a"=>100, "b"=>200}
```


## pluralize
複数形にするメソッド。


## githubからファイル直接ダウンロード
ファイルのページをgithubで開き、そのURLをそのままコピーする。その後、  
`curl [url] -O` でファイルごと追加される。


## find_or_create
```
def find_or_create_labeled_data_set_question!(question_condition_id)
	question_condition = QuestionCondition.find_by(unique_id: question_condition_id)
	labeled_data_set_question = LabeledDataSetQuestion.find_by(labeled_data_set: self)
	return labeled_data_set_question if labeled_data_set_question

	LabeledDataSetQuestion.create!(labeled_data_set: self, question_condition: question_condition, question: question_condition.question)
end
```
上のコードでは実際にfind_or_createメソッドを定義しているが、これは実際はrailsの`find_or_create_by`メソッドに  
ちなんでいる。上コードでみると、最初にfindして、あったらreturnし、なかったらその下のcreateをするという仕組み。


## gitでaddする前のファイル復元
`git status`とやると、前のコミットから何のファイルを消したかなどがみれる。  
その中から復元したいファイルを選び以下のコマンドを実行。
```
git checkout [file]
```

## gitでaddした後のファイル復元
```
// 直前のコミットから、ファイルを復元する
git checkout HEAD deleted-file.php


// 特定のコミットからファイルを復元する
git checkout HEAD deleted-file.php
```


## React Developer Tool
chromeの拡張機能で、ページを開いてクリックするとそのページがReactを使っているか  
どうかなどがわかる。本来はReactの開発用。


## RailsからAWSクライアントを使う
AWS S3(ストレージの種類)クライアントのインスタンス化
```
client = Aws::S3::Client.new
```
ストレージにアップロード
```
client.put_object(acl: "", bucket: "", key: "", body: "")
```
ここで、アップロードはあるURLにアップロードするわけだが、そのURLの一部  
がbucketとkeyで、以下のような構成になっている。
```
https://${bucket}.s3-ap-northeast-1.amazonaws.com/${key}
```
bodyはアップロードするデータ自体で、aclはなんか属性的なもの。


## git(リモート)のコミット文修正
まずローカルの直前のコミットメッセージを以下のコマンドで修正。
```
git commit --ammend
```
次に、普通にpushするとエラー起きるので、`-f`をつけてpushする。
```
git push origin [branch] -f
```


## git branch間の違いを比較
branch1とbranch2の違いを確認したい時、以下のコマンドを実行。
```
git diff [branch1] [branch2]
```


## lsで下二つのファイルを消す
```
ls | tail -n2 | xargs -n1 rm
```


## returnの省略
railsではreturnは省略できる


## ActiveSupport::Concern
```
require 'active_support'

module MyConcern
  #モジュールの中でActiveSupport::Concernモジュールをエクステンドする
  extend ActiveSupport::Concern

  def my_instance_method
    "my_instance_method()"
  end

  #ClassMethodsモジュールの中で、インクルーダーに追加するクラスメソッドを定義する
  module ClassMethods
    def my_class_method
      "my_class_method()"
    end
  end
end

class MyClass
  #ActiveSupport::Concernをエクステンドしたモジュールをインクルードする
  include MyConcern
end


obj = MyClass.new
obj.my_instance_method  #=> "my_instance_method()" 

#MyClassに対してMyConcern::ClassMethodsで定義したメソッドを呼び出す
MyClass.my_class_method #=> "my_class_method()"　#インクルーダーにクラスメソッドとして追加できている！
```
ConcernをExtendしたmoduleをincludeすると、そのmoduleのクラスメソッドも使えるようになる。  
これを使うとと処理を共通化でき、以下のようにDRYに書ける。

before:
```
class ArticlesController < ApplicationController
  before_action :set_categories
 #中略

  def set_categories
    @categories = Category.all
  end
 #中略
end
```
```
class DashboardsController < ApplicationController
 before_action :set_categories
 #中略

  def set_categories
    @categories = Category.all
  end
 #中略
end
```

after:
```
module CommonActions
 #以下extendは必須
  extend ActiveSupport::Concern

  def set_categories
    @categories = Category.all
  end

end
```
```
class ArticlesController < ApplicationController
 #以下includeは必須
  include CommonActions

  before_action :set_categories
  #中略
end
```
```
class DashboardsController < ApplicationController
 #以下includeは必須
  include CommonActions

  before_action :set_categories
  #中略
end
```


## freeze
変更不可能(イミュータブル)にするためのメソッド。
```
PERMIT_ID = ['0001', '0002', '1234']
PERMIT_ID.freeze
PERMIT_ID.reject! { |id| id == '1234' }
# => RuntimeError: can't modify frozen Array

p PERMIT_ID
# => ["0001", "0002", "1234"]
```


## gitのadd取り消し
```
git reset HEAD [filepath]
```


## factory_botのcreate_list
```
create_list(<作成したいファクトリ名>, <作成するインスタンス数>, *<トレイトやオーバーライドしたい項目>)
```
具体例は以下
```
create_list(:user, 3, :admin, :male, name: "Jon Snow")
```
