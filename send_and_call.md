# sendメソッド


## sendメソッドとは
レシーバの持っているメソッドを呼び出してくれるメソッド。


## 引数一つの記法
```
user = User.new(name: "taro")

user.send(:name) # => taro
user.send("name") # => taro
```


## 引数二つの記法
```
class User
  def age(num)
    puts "私は#{num}歳です"
  end
end

user = User.new

# 定義したメソッドを呼び出す
user.age(20) # => 私は20歳です

# sendを使った書き方
user.send("age", 20) # => 私は20歳です
user.send(:age, 20) # => 私は20歳です
```


## カラムに値を入れる方法
```
user = User.new
user.send("age=", 30)

#出力結果
user.age
=> 30
```


## sendメソッドのメリット

sendを使わない場合
```
# 値によって実行するメソッドを分岐させる
class Shape
  def create_shape(shape_type)
    case shape_type
    when "circle" then
      create_shape_circle
    when "square" then
      create_shape_square
    when "triangle" then
      create_shape_triangle
    end
  end
end

shape = Shape.new
shape.create_shape("circle")
```

sendを使った場合
```
class Shape
  attr_accessor :shape_type

  def initialize(shape_type)
    @shape_type = shape_type
  end
end

shape = Shape.new("circle")
shape.send("create_shape_#{shape.shape_type}")
```




# callメソッド


## callメソッドとは
基本的にsendメソッドと同じ。
```
mii.send(:meow, 3)
mii.method( :meow ).call( 3 )
```


## call と send の違い

| 方法        | メソッドが存在しなかった場合の挙動 |
| ----------- | ---------------------------------- |
| Method#call | エラー                             |
| Object#send | Object#method_missing が実行される |

```
# 存在しないメソッドを実行 / 後者はエラーになる
mii.send(:undefined, 3)
mii.method( :undefined ).call( 3 )
```
また、以上のようにインスタンスに対して使うのか、メソッドに付加するのかという違いもある。



