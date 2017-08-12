# 拡張可能レコード
標準のレコードが持つ問題点を解決したいくつかのデータ構造・ライブラリ

## 標準のレコードが持つ問題点
出典: <http://nikita-volkov.github.io/record/>

1. 名前問題が解決できていない。つまり同じモジュールの中で同じフィールド名をもつ２つのレコードを定義できない。例えば以下はコンパイルできない。

```hs
data A = A { field :: String }
data B = B { field :: String }
```

2. 部分的である。以下のコードはコンパイル時に何も警告しないのにランタイム時にエラーになる。

```hs
data A = A1 { field1 :: String } | 
         A2 { field2 :: String }

main = print $ field1 $ A2 "abc"
```

3. 型コンストラクタ間で違う方を持つ同じ名前のフィールドを定義することが出来ない。

```hs
data A = A1 { field :: String } | 
         A2 { field :: Int }
```

4. ネストしたレコードの値を更新することは指数関数的に面倒くさくなる

```hs
addManStk team = team {
  manager = (manager team) {
    diet = (diet (manager team)) {
      steaks = steaks (diet (manager team)) + 1
    }
  }
}
```

## OverloadedRecordFields
GHCの言語拡張でレコードの問題を解決しようという試み

* [ghc-proposals - Overloaded Record Fields](https://github.com/ghc-proposals/ghc-proposals/blob/master/proposals/0002-overloaded-record-fields.rst)
* [GHC Wiki - OverloadedRecordFields](https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields)
* [OverloadedRecordFields revived](http://www.well-typed.com/blog/2015/03/overloadedrecordfields-revived/)

すべての機能が入るまでの道のりは３ステップに分けられる

### Step1. DuplicateRecordFields
* [GHC Wiki - DuplicateRecordFields](https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields/DuplicateRecordFields)

同じモジュール内で同じフィールド名をもつレコードを定義できるようにする言語拡張

```hs
data Person  = MkPerson  { personId :: Int, name :: String }
data Address = MkAddress { personId :: Int, address :: String }
```

GHC 8.0 で既に実装されている

### Step2. OverloadedLabels
* [GHC Wiki - OverloadedLabels](https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields/OverloadedLabels)

`fromLabel @"field" :: alpha` の糖衣構文として `#field` のような書き方ができるようになる。 `fromLabel` は以下の型クラス `IsLabel` のメソッド。

```hs
class IsLabel (x :: Symbol) a where
  fromLabel :: Proxy# x -> a
```

* [GHC.OverloadedLabels](https://hackage.haskell.org/package/base-4.10.0.0/docs/GHC-OverloadedLabels.html)

### Step3. Magic type classes
* [GHC Wiki - Magic classes for overloaded record fields](https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields/MagicClasses)

TODO

## ライブラリ
レコードのように扱える独自のデータ構造を使って標準のレコードが持つ問題を解決しようという試み

* [record](http://hackage.haskell.org/package/record)
  * [Announcing the first class records library](http://nikita-volkov.github.io/record/)
  * [Anonymous records in Haskell](http://www.techcast.com/events/bigtechday8/maffei-1005/?q=maffei-1005)
* [odr/pers](https://github.com/odr/pers)
* [重複したフィールドラベル](http://d.hatena.ne.jp/kazu-yamamoto/20160114)
* [sebastiaanvisser/fclabels](https://github.com/sebastiaanvisser/fclabels)
* [Frames Tutorial](http://acowley.github.io/Frames/)
* [rawr: Anonymous extensible records](https://hackage.haskell.org/package/rawr)
* [Bookkeeper](https://turingjump.com/blog/bookkeeper/)
* [Bookkeeper.Permissions](https://hackage.haskell.org/package/bookkeeper-permissions-0.1.0.0/docs/Bookkeeper-Permissions.html)
* [割とすぐに始められるextensibleチュートリアル(レコード編)](http://fumieval.hatenablog.com/entry/2016/10/10/000011)
* [Records are Sheaves of Types.](http://www.jonmsterling.com/posts/2014-05-11-records-are-sheaves-of-types.html)
* [Extensible Records Explained](https://www.schoolofhaskell.com/user/fumieval/extensible-records)
* [Control Flow in Haskell (0) - Introduction](http://hsyl20.fr/home/posts/2016-12-12-control-flow-in-haskell-part-0.html)
* [波打たせるものの正体(エクステンシブル・タングル)](http://fumieval.hatenablog.com/entry/2016/12/18/181540)

### Vinyl
* [vinyl](https://hackage.haskell.org/package/vinyl)
* [Programming in Vinyl / BayHac 2014](https://vimeo.com/95694918)
* [Vinyl: Records in Haskell and Type Theory](https://vimeo.com/102785458)