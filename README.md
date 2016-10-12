# a1
My private language #1.

```
# 命名(左辺のオブジェクトを右辺の名前(変数名)で呼ぶ、と宣言する)
123 = number
3.14 = pi
"hello" = string

# 演算
1 + 2 = a # 3
a * 4 = b # 12
b % 5 = c # 2
c / 3     # 1
a == 3    # true
a != 3    # false
1 + 2 * 2 # 6 電卓の入力と同様、左から評価する

# 多重代入と演算
123 = num1 = num2
10 = a * 2 = b * 2 = c  # a は 10, b は 20, c は 40

# リスト
1 2 3 = ary
( # () で囲んで改行でもいい
  1
  2
  3
) = ary

ary.pop  # => 3 破壊的
4 >> ary # push、破壊的

# レンジ
1..3 = ary

# 名前付き要素を持つリスト(ハッシュ)
("bob" = name 20 = age) = hash
(
  "bob" = name
  20    = age
) = hash

# ()を省略したら多重代入と区別できるかなー
10 = a 20 = b 30 = c = hash

hash.pop # => 20 = age 破壊的 # 複数の名前持ってたらどうなるのこれ
"Japan" = country >> hash # push 破壊的
                          # countryというシンボルがついた値"Japan"を追加してる

# リスト参照
ary.0       # 1
hash.name   # "bob"

# イテレータ
ary | each { .0 | out }
ary | eachi { "${i}: ${.}" | out }
hash | each { key value ; "${key}: ${value}" | out }

# key value; は仮引数。実態はシンボルリスト。
# ; 文の終わりを指定

# 制御構文(IF)
ary | if { .0 == .1 } "true" "false" | out
ary | if { .0 == .1 } {
  "true" = a
} {
  "false" = a
}
# else 時の処理はもちろん optional
# 評価順の関係で条件式の {} は外せないだろうなあ…
# if をキーワード化する手もあるけど

# 三項演算子的なもの、連結子の ? を利用
ary | { .0 == .1 } ? "true" "false" | out

# caseは？
# with シェルスクリプトだと面倒な入出力の枝分かれ

# while
ary | while { .size >= 1 } {
  ary.pop | out
}
# break
ary | while true {
  if { .size <= 0 } { break }
  ary.pop | out 
}
# break/next まで実装する？既存言語のトレースだし新文法でやる意味なさそう
# 最悪、イテレータと IF 文があればどうにでもなりそう


# メソッド定義
{
  x y     # シンボルリストが冒頭であれば仮引数宣言として解釈。
          # 与えられた引数が .0 = x .1 = y と命名される。オプショナル。
  x ** y  # return する値。最後の式の評価結果がメソッドの戻り値。
} = pow   # 命名する変数名

# () は定義時評価に対し、{} は実行時評価。
# ただし {} 内の子 () は直上の親 {} が実行時に定義される。

# メソッド呼び出し、結果に命名
pow 2 3 = ans

# 無名メソッドの即時実行
{ . * 2 } 3 # 6

# 前の結果を次の引数へ(.は直前の結果を参照する自動変数)
pow 2 2 | pow . 2 # 16
# 結果がリストの場合は添字展開が可能
5 2 | pow .0 .1 # 25

# メソッドのディープコピー
pow > pow2
{ . / 2.0 } >> pow2 # 最後(つまり戻り値)の挙動を後から追加

# オブジェクト型(クラス的なもの、これは遅延評価ハッシュ)
{
  "" = name
  0  = age
  { "my name is ${.name}" | out }     = say_name
  { "I am ${.age} years old." | out } = say_age
} = Hoge

# Hoge クラスのインスタンスを生成
Hoge > hoge_instance # 実は Hoge をディープコピーして返してるだけのような気がする

# メソッド(特異)を追加
{ "hello" | out } = say >> hoge_instance

# メソッドの呼び出し
hoge_instance.say

# てか、これでいい
hoge_instance | "I am ${.age} years old." | out

# さらに
{ "I am ${.age} years old." | out } = say
hoge_instance | say

# 変数名にぶら下げるメソッドは破壊的なものだけにした方が良さそう
# 言語仕様じゃなくてコーディングマナーで

# クラス(という名の実体)とインスタンス(実体)の区別がないので(あった方がいい？)
# 今のところ↓を実行しても hoge_instance には影響がまったくない
{ "I'm Hoge!!!" | out } = say >> Hoge

# 自分が生成された際の元クラス(実体)への参照は > 時に
# 自動変数を付与してあげて、たどれてもいいかも

# 空のオブジェクト型(クラス)のインスタンスを作成
{ } > noname_instance

# アドレス参照？
Hoge =& hoge_address

# 連結子
# =  命名
# |  パイプ
# >  ディープコピー
# >> 追加
# ?  条件式
```
