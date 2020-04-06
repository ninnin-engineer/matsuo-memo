# sidekiq(worker)


## sidekiqとは
Rails アプリで非同期処理を行うためのライブラリ。複数のジョブを同時に実行することができ、メモリを節約する。  


## 導入方法
redisが必要なのでインストールをする。Redis はジョブの管理に使われています。
```
brew install redis
```
その後、
```
vim Gemfile # gem 'sidekiq' を追加
bundle install
```


## Worker処理の記述
非同期処理を行うクラスは`Sidekiq::Worker`をincludeして作る。  
あとはperformメソッドの中に非同期でしたい処理を書く(引数は自由)。  
`workers`フォルダの中に書く。
```
# app/workers/hard_worker.rb
class HardWorker
  include Sidekiq::Worker
  def perform(name, count)
    puts 'Doing hard work: name=' + name + ', count=' + count.to_s
  end
end
```


## sidekiqの起動と呼び出し
まず、sidekiqのコンソールを起動する
```
bundle exec sidekiq
```

呼び出すには、`rails c`後に、
```
HardWorker.perform_async('Classmethod', 1)
```


## 時間差で実行
以下は3時間後に実行する設定。デフォルトでは 15 秒間隔で確認しにいきます。	
```
HardWorker.perform_in(3.hours, 'Classmethod', 1)
```


## sidekiqをブラウザで監視

### 必要なGem
```
gem 'slim'
gem 'sinatra', '>= 1.3.0', :require => nil
```

### routes.rbの編集
```
require 'sidekiq/web'
mount Sidekiq::Web => '/sidekiq'
```
