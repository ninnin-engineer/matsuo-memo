# rspec
[rspecいいサイト](https://qiita.com/jnchito/items/42193d066bd61c740612)  


## describe/it/expect
```
RSpec.describe '四則演算' do
  describe '足し算' do
    it '1 + 1 は 2 になること' do
      expect(1 + 1).to eq 2
    end
  end
  describe '引き算' do
    it '10 - 1 は 9 になること' do
      expect(10 - 1).to eq 9
    end
  end
end
```
一つの`describe`の中に複数の`it`はよくあるが、複数の`expect`はあまり書かない。  


## describeで文字列以外
```
RSpec.describe User do
  describe '#greet' do
    it '12歳以下の場合、ひらがなで答えること' do
      user = User.new(name: 'たろう', age: 12)
      expect(user.greet).to eq 'ぼくはたろうだよ。'
    end
    it '13歳以上の場合、漢字で答えること' do
      user = User.new(name: 'たろう', age: 13)
      expect(user.greet).to eq '僕はたろうです。'
    end
  end
end
```
describeには文字列じゃなくてクラスもかける。  
また、ネストしたdescribeにはメソッド名で`'#greet'`のように書くことが多い。


## context/before
```
RSpec.describe User do
  describe '#greet' do
    before do
      @params = { name: 'たろう' }
    end
    context '12歳以下の場合' do
      it 'ひらがなで答えること' do
        user = User.new(@params.merge(age: 12))
        expect(user.greet).to eq 'ぼくはたろうだよ。'
      end
    end
    context '13歳以上の場合' do
      it '漢字で答えること' do
        user = User.new(@params.merge(age: 13))
        expect(user.greet).to eq '僕はたろうです。'
      end
    end
  end
end

```
contextはdescribeと同じようにグループ分けようのもの。条件分岐みたいに使われることが慣習。  
beforeはテストを実行する前の共通処理やデータのセットアップ等を行う。  
beforeはネストしたdescribeやcontextの中でも使える。


## let
```
RSpec.describe User do
  describe '#greet' do
    let(:params) { { name: 'たろう' } }
    context '12歳以下の場合' do
      before do
        params.merge!(age: 12)
      end
      it 'ひらがなで答えること' do
        user = User.new(params)
        expect(user.greet).to eq 'ぼくはたろうだよ。'
      end
    end
    context '13歳以上の場合' do
      before do
        params.merge!(age: 13)
      end
      it '漢字で答えること' do
        user = User.new(params)
        expect(user.greet).to eq '僕はたろうです。'
      end
    end
  end
end
```
`let(:foo) { ... }`のように書くと、`{ ... }`の中の値が`foo`として参照できるというのがletの使い方。  
外側の` { } `はRubyのブロックで、 内側の` { } `はハッシュリテラル。これを避けるわかりやすい別の記法が以下。
```
let(:params) do
  hash = {}
  hash[:name] = 'たろう'
  hash
end
```
他にも、`let(:user) { User.new(params) }`のようにすることも可能(`user`でアクセス可能になる)。  
また、letは遅延評価される。`expect(user.greet).to`が呼ばれる => `user`って何だ？ => let(:user)が評価。


## subject/is_expected
上のコードの`user.greet`を以下のようにしてDRYにすることができる。  
```
RSpec.describe User do
  describe '#greet' do
    let(:user) { User.new(params) }
    let(:params) { { name: 'たろう', age: age } }
    subject { user.greet }
    context '12歳以下の場合' do
      let(:age) { 12 }
      it 'ひらがなで答えること' do
        is_expected.to eq 'ぼくはたろうだよ。'
      end
    end
    context '13歳以上の場合' do
      let(:age) { 13 }
      it '漢字で答えること' do
        is_expected.to eq '僕はたろうです。'
      end
    end
  end
end
```


## it/specify/example
```
# それ(it)はユーザー名を返す
it 'returns user name' do
  # ...
end

# 会社は社員を持つことを仕様として明記(specify)する
specify 'Company has employees' do
  # ...
end

# fizz_buzzメソッドの実行例(example)
example '#fizz_buzz' do
  # ...
end
```
これら３つは全部同じものだが、文脈をわかりやすくするために用意されてる。


## shared_example/it_behaves_like
```
RSpec.describe User do
  describe '#greet' do
    let(:user) { User.new(name: 'たろう', age: age) }
    subject { user.greet }

    shared_examples '子どものあいさつ' do
      it { is_expected.to eq 'ぼくはたろうだよ。' }
    end
    context '0歳の場合' do
      let(:age) { 0 }
      it_behaves_like '子どものあいさつ'
    end
    context '12歳の場合' do
      let(:age) { 12 }
      it_behaves_like '子どものあいさつ'
    end

    shared_examples '大人のあいさつ' do
      it { is_expected.to eq '僕はたろうです。' }
    end
    context '13歳の場合' do
      let(:age) { 13 }
      it_behaves_like '大人のあいさつ'
    end
    context '100歳の場合' do
      let(:age) { 100 }
      it_behaves_like '大人のあいさつ'
    end
  end
end

```
同じexampleを上記のようにまとめて書くことができる。  
`shared_examples 'foo' do ... end`で再利用したいexampleを定義し、`it_behaves_like 'foo'`で定義したexampleを呼び出すイメージです。


## shared_context/include_context
```
RSpec.describe User do
  let(:user) { User.new(name: 'たろう', age: age) }
  shared_context '12歳の場合' do
    let(:age) { 12 }
  end
  shared_context '13歳の場合' do
    let(:age) { 13 }
  end

  describe '#greet' do
    subject { user.greet }
    context '12歳以下の場合' do
      include_context '12歳の場合'
      it { is_expected.to eq 'ぼくはたろうだよ。' }
    end
    context '13歳以上の場合' do
      include_context '13歳の場合'
      it { is_expected.to eq '僕はたろうです。' }
    end
  end

  describe '#child?' do
    subject { user.child? }
    context '12歳以下の場合' do
      include_context '12歳の場合'
      it { is_expected.to eq true }
    end
    context '13歳以上の場合' do
      include_context '13歳の場合'
      it { is_expected.to eq false }
    end
  end
end
```
`shared_context 'foo' do ... end`で再利用したいcontextを定義し、`include_context 'foo' `で定義したcontextを呼び出す。


## 遅延評価されるletと事前に実行されるlet!
letは遅延評価されるが、let!だと普通の変数宣言と同じように事前に読み取られる。  
以下のコードは普通のletだとエラーが起きる。
```
RSpec.describe Blog do
  let!(:blog) { Blog.create(title: 'RSpec必勝法', content: 'あとで書く') }
  it 'ブログの取得ができること' do
    expect(Blog.first).to eq blog
  end
end
```


## skip
```
RSpec.describe '何らかの理由で実行したくないクラス' do
  it '実行したくないテスト' do
    expect(1 + 2).to eq 3

    skip 'とりあえずここで実行を保留'
    # ここから先は実行されない
    expect(foo).to eq bar
  end
end
```
実行を途中で止めてpendingとマークする。


## xit/xexample/xspecify/xdescribe/xcontent
```
RSpec.describe '何らかの理由で実行したくないクラス' do
  xit '実行したくないテスト' do
    expect(1 + 2).to eq 3

    expect(foo).to eq bar
  end
end
```
xをつけると実行されなくなる。pending扱いになる。


## to/not_to/to_not
であることを期待するのがtoなのに対して、そうじゃ無いことを期待するのが`not_to`と`to_not`。


## マッチャとは
```
expect(1 + 2).to eq 3
expect([1, 2, 3]).to include 2
```
`expect(...).to xxx`の`to`の直後に出てくるやつ（xxxの部分）がマッチャ。

### be
```
expect(1 + 2).to be >= 3
expect([message_1].first).to be message_2
```
不等号の時とか、同じインスタンスかどうか確認したい時にしよう。

### be_xxx (predicateマッチャ)
`empty?`のようにメソッド名が「?」で終わり、戻り値がtrue/falseになるメソッドを`be_empty`のような形式で検証できる。
```
expect([]).to be_empty

user = User.new(name: 'Tom', email: 'tom@example.com')
expect(user).to be_valid
```

### be_truthy/be_falsey
「?」で終わらないが、戻り値としてtrue/falseを返すメソッドは`be_truthy/be_falsey`というマッチャで検証する
```
# 必須項目が入力されていないので保存できない（結果はfalse）
user = User.new
expect(user.save).to be_falsey

# 必須項目が入力されているので保存できる（結果はtrue）
user.name = 'Tom'
user.email = 'tom@example.com'
expect(user.save).to be_truthy
```
`be true`や`be false`などとの違いは以下。
```
# どちらもパスする
expect(1).to be_truthy
expect(nil).to be_falsey

# どちらも失敗する
expect(1).to be true
expect(nil).to be false

# be の代わりに eq を使った場合も同様に失敗する
expect(1).to eq true
expect(nil).to eq false
```

### change
`expect{ X }.to change{ Y }.from(A).to(B) `＝「X すると Y が A から B に変わることを期待する」
```
x = [1, 2, 3]
expect{ x.pop }.to change{ x.size }.from(3).to(2)
```
`by`を使用すると以下のようにかける。
```
x = [1, 2, 3]
expect{ x.push(10) }.to change{ x.size }.by(1)
```
```
it 'userを削除すると、userが書いたblogも削除されること' do
  user = User.create(name: 'Tom', email: 'tom@example.com')
  # user が blog を書いたことにする
  user.blogs.create(title: 'RSpec必勝法', content: 'あとで書く')

  expect{ user.destroy }.to change{ Blog.count }.by(-1)
end
```

### raise_error
```
expect{ 1 / 0 }.to raise_error ZeroDivisionError
```


## require 'rails_helper'
`require 'rails_helper'`と記述すると、`/app/helpers/`以下のファイルで定義したメソッド等をRspecのテストファイル上で使うことができるようになる。


## Rspec Request
APIに関するテストをする。APIに対するリクエストに関してテストですので`rspec/requests/`配下に作成する。  
[参考URL](https://qiita.com/k-penguin-sato/items/defdb828bd54729272ad)  

### リスポンスのjson
```
get '/api/v1/posts'
json = JSON.parse(response.body)
```

### ステータスコード
```
# リクエスト成功を表す200が返ってきたか確認する。
expect(response.status).to eq(200)
```

### データの個数
```
# 正しい数のデータが返されたか確認する。
expect(json['data'].length).to eq(10)
```

### postリクエスト
```
require 'rails_helper'

describe 'PostAPI' do
 it '新しいpostを作成する' do
    valid_params = { title: 'title' }

    #データが作成されている事を確認
    expect { post '/api/v1/posts', params: { post: valid_params } }.to change(Post, :count).by(+1)

    # リクエスト成功を表す200が返ってきたか確認する。
    expect(response.status).to eq(200)
  end
end
```

### putでデータ更新されてるかの確認
```
#データが更新されている事を確認
expect(json['data']['title']).to eq('new-title')
```

### deleteでデータ消えるかの確認
```
#データが削除されている事を確認
expect { delete "/api/v1/posts/#{post.id}" }.to change(Post, :count).by(-1)
```


## Rspecのchangeの記法に関する注意
```
expect do
	send_request
end.to change(
	HotelArrangementRefund, :count
).by(1)
```
```
expect do
	send_request
end.to change { HotelArrangement.count }.by(1)
```
changeの中の表記方法が上と下でややこしいので注意。


## Rspecでのデバッグ方法

### ppで変数出力
テストコードでは`logger.info`などは使えないが、`pp [変数]`と書くことによって`rspec`を実行した  
ターミナル上に変数の値が出力できる。`it do ~ end`の中に書くことで表示されるが、`describe`内とかだと表示されない。

### ログの確認
`development.log`ではなく`test.log`の方に書いてあるので注意。



## create
```
let(:product) { create(:product) }
```
このコードは以下のコードの省略形みたいな物。
```
@product =  FactoryBot.create(:product)
```
しかしこれはデフォルトでは使えず、`rails_helper.rb`に以下のように書く必要がある。
```
require 'spree/testing_support/factories'
```


## 特定のdescribeだけテスト実行
```
Rspec.describe Test, type: :model do
  describe "#testA", type: :doing do
    context "pattern-a" do
       ...
    end
  end

  describe "#testB" do
    context "pattern-a" do
      ...
    end
  end
end
```
以上のように`type: :doing`を追加して、以下のコマンドを実行。
```
rspec --tag type:doing [path]
```

