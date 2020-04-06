# Railsのパーセント記法


## %,%Q
ただの文字列だが、シングルクオートやダブルクオートのエスケープが不要になる。
```
str = %(Programming language "Ruby")
puts str
# => Programming language "Ruby"

ruby = "Ruby"
str2 = %(Programming language "#{ruby}")
puts str2
# => Programming language "Ruby"
```
また、注意てとしてここでは丸括弧()を使用しているが、パーセント記法は括弧の  
の種類はどうでもいい。


## %q
シングルクオートで囲うのと同様の振る舞いをする。  
シングルクオートなので変数展開がされない。
```
ruby = "Ruby"
str = %q(Programming language "#{ruby}")
puts str
# => Programming language "#{ruby}"
```


## %w
配列を作る。要素間は空白で区切る。  
式の展開はされない。
```
array = %w(one two three four)
p array
# => ["one", "two", "three", "four"]
```


## %W
%wと違い、式が展開される。
```
ruby = 'Ruby'
PYTHON = 'Python'

array = %W(#{ruby} #{PYTHON} PHP)
p array
# => ["Ruby", "Python", "PHP"]
```


## %i
要素がシンボルの配列を作る。式は展開されない。
```
array = %i(Ruby Python PHP)
p array
# => [:Ruby, :Python, :PHP]
```


## %I
%iとは違い式が展開される。
```
ruby = 'Ruby'
PYTHON = 'Python'

array = %I(#{ruby} #{PYTHON} PHP)
p array
# => [:Ruby, :Python, :PHP]
```


## %x
コマンド出力を行う。バッククオートで囲うのと同じ役割。
```
res = %x(date) # dateコマンドの実行
puts res
# => Sat Aug 23 23:27:01 JST 2014
```


## %s
式の展開はされないシンボル。
```
sym = %s(Ruby)
p sym
# => :Ruby
```
