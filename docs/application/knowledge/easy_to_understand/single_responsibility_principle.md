# メソッドの単一責任化について

---
# メソッドの単一責任化とは
- ひとつのメソッドで2つ以上のことをしない（２つ以上の責任を負わない）ようにすること。
- あるメソッドが2つ以上のことをしている場合、ひとつになるようにメソッドを分割する。

---
# 例
以下のようなケースのとき。

* 店舗の在庫管理システム。
* 商品情報（マスター情報、在庫情報）を追加したい。

## データベーステーブルの想定
  * 商品マスターテーブル
  * 在庫テーブル

## 作成中のコード
次ページのようなコードを書いたが、イマイチな気がする。

---
### 呼び出され側
```
class ProductStock {
  public static function registerMasterAndAddStock(
    string $code,
    int $num,
    array $master_info = []
  ){
    if (/* 商品マスターに登録されていない */) {
      //商品マスターに登録する処理
    }
    //在庫を追加する処理
  }
}
```

### 呼び出し側 : 「2B鉛筆」をマスター登録し、在庫を3つ追加する
```
ProductStock::registerMasterAndAddStock(
  'pencil_2b', 3, ['name' => '2B鉛筆']);
```

---
# 悪い点
* ProductStock::registerMasterAndAddStock() メソッドは3つのことをやっている。
  * 商品マスターに追加済みかどうか調べる
  * 商品マスターへの登録
  * 在庫への追加
* このメソッドは次のいずれのケースにおいても修正が求められる。
  * マスターに登録済みかどうかの確認方法が変更になったとき
  * マスターへの登録方法が変わったとき
  * 在庫の追加方法が変わったとき
* これらは「ひとつのメソッドでひとつのことをやる」ように分割するべき。

---
# メソッドの単一責任化のメリット
- クラスの性質が明らかになる
    - それぞれのメソッドが単一の目的を果たすようになることによって、クラスが行うこと全体が明確になる
    - メソッドがクラスの振る舞いのリストになるため
- コメントする必要がない
    - メソッドの名前がコメントの代わりになるため
- 再利用を促進する
    - 他のメンバーがコードの複製ではなく、切り出したコードの再利用をするようになる
- ほかのクラスへの移動が楽
    - 設計の改善に対するコストを低くする

---
# 単一責任が重要な理由
- 変更が簡単なアプリケーションは「再利用が簡単なクラス」によって構成される
    - 再利用が簡単なクラスは着脱可能なユニット
    - 2つ以上の責任を持つクラスは複雑で、必要な部分だけを再利用することが難しい
- 再利用できないコードは複製して使われてしまうので、変更箇所が増えてしまう
- コードをDRYに保つ、といことはクラスやメソッドの単一責任化と言い換えることができる
- 単一責任のクラスを作ると、どんな些細な振る舞いもそれがただ一箇所のみに存在するようになる
    - Don’t Repeat Yourselfはこの概念を一言に言い表している
- クラスがすることはすべてそのクラスに関連すること

---
# クラスが単一責任の設計になっているかの見極め方
- あたかも知覚があるかのように仮定して問いただす
    - OK：「ギアさん、あなたのギア比を教えてください」
    - NG：「ギアさん、あなたのタイヤのサイズを教えてください」（ギアがタイヤを持っているわけではない）
- 考えつく限りの短い一文でクラスを説明してみる
    - その文章に「それと」が含まれている場合、２つ以上の責任を持っている可能性が高い
    - 「または」がつくと、２つ以上の責任をもっていて、かつそれらが関連していない可能性が高い

---
# 商品在庫/マスター登録のコードを修正する
* 知覚があるかのように問いただす
  * 商品在庫さん、商品マスターに「2B鉛筆」が登録されているか教えてください。
    → ヘンテコな問いになっているように感じる。
  * 商品在庫さん、「2B鉛筆」の現在の在庫数を教えてください。
    → 妥当な質問だと思われる。
* 短い文で説明する
  * 商品マスターに登録されているか確認する。登録されていなければ追加する。それと、在庫を追加する。
    →複数の責任を持っていそうだ。

---
# プログラムの修正
* 責任ごとに処理を分割する。
  * 商品マスターに登録済みか調べる
  * 商品マスターに追加する
  * 在庫数を加算する

---
### 呼び出され側
```
class ProductMaster {
  public static function has(string $code): bool{
    //商品マスターに登録済みかどうかを返す
  }
  public static function register($code, $name){
    //商品マスターに登録する処理
  }
}
```

```
class ProductStock {
  public static function add(string $code, int $num){
    //在庫を追加する処理
  }
}
```

### 呼び出し側 : 「2B鉛筆」をマスター登録し、在庫追加
```
if (! ProductMaster::has('pencil_2b')) {
  ProductMaster::register('pencil_2b', '2B鉛筆');
}
ProductStock::add('pencil_2b', 3);
```

---
# 改善された点
* 個々のメソッドがわかりやすくなった。
  * メソッドのリストがクラスの振る舞いを表現している。
* 変更の際に修正しなくてはならない範囲が限定された。
  * 商品マスターへの登録方法が変更された際
    * ProductMaster::register() を修正。
    * ProductStock::add()には影響がない。
  * 在庫の追加方法が変更された際
    * ProductStock::add()を修正。
    * ProductMaster::register()には影響がない。

↓
修正によって、より「変更が容易」になった。

↓
より良いコードになったと言える。