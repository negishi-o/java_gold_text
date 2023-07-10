# Java
<br>

## 例外処理

--------------

<br>

### ***〇try-with-resourese***

***■利用POINT***

・try()内に記述出来るのは、 ***<span style="color: red">java.lange.AutoClose、Closeable</span>*** のみ

`java.lange.AutoClose`

↓↓↓インターフェースを継承クラスに利用する事

```java
void close() throws Exception()
```

`java.lange.Closeable`

↓↓↓インターフェースを継承クラスに利用する事

```java
void close() throws IOException()
```

更に`Throwableクラス`を利用して機能拡張が可能!!

使いたい時は<span style="color: red">getSuppressed()</span>メソッドでclose()メソッド内で定義した例外も受け取りが可能になる。

```java
class Foge implements AutoCloseble {
  public void close() throws SQLException {
    //このclose()内で定義したエラーメッセージも取得したい!!
    throw new SQLExeption("Hellow")
  }
}

public class Main {
  pulic static void main(String[] args) {
    //処理色々
    try(Foge obj = new Foge()) {
      //処理色々
    } catch (SQLExeption e) {
      //このgetSuppressed()メソッドで取得出来るよ♪
      Throwable[] errAry = e.getSuppressed();
      for(Throwable err: errAry) {
        System.out.println(err.getMessage());
      }

    }
  }
}
```

〇アサーション

⇒正しい動作を保証する機能

■利用POINT

コードはどちらかを挟めまばOK！！

```java
assert boolean hoge;//hogeに条件式
assert boolean hoge : Message; //hogeに条件式でMessageがあると、false判定時にエラーとして出力
```

コンパイル時には通常アサーション機能が無効になっているので、  
実行時には ***<span style="color: red;">-ea</span>*** を付ける事

## コレクションとジェネリクス

--------------

〇そもそも、コレクションとジェネリクスとは何か?

⇒<span style="color: yellow;">コレクション</span>は複数のオブジェクトを纏めて取り扱う為の、統一した考え  
とりあえず、 `List,Set,Queue,Map` がコレクションと覚えておく事  
※正確にはMapはコレクションから派生したものではないが

⇒<span style="color: yellow;">ジェネリクス</span>はクラス定義などで汎用的に型を示しおき、利用時に目的のオブジェクトの型を当てはまる事で正しいコードを形成出来る

何を言ってるか、分からないので、簡単に言えばこんな感じ
パターン①だとコンパイルは出来る。途中で参照型でIntegerを突っ込んでるけど。  
ただ、実行時にはエラーが吹く

```java
//パターン①
List list = new Arraylist()
list.add("Hey!!"); list.add("Bye!!");
list.add(new Integer(777));
for(int i = 0; i<list.size(); i++) {
  System.out.println((String)list.get(i));
}
```

これをコンパイル時に防ぐ為に、<span style="color: yellow;">ジェネリクス</span>が活躍する。以下の様に、する事でコンパイル時にエラーが特定可能だぁ!!

```java
//ジェネリクス使った時
List<String> list = new Arraylist<String>()
//List<String> list = new ArrayList<>() これでも行ける
list.add("Hey!!"); list.add("Bye!!");
list.add(new Integer(777)); //←コンパイルエラー発生！！
for(int i = 0; i<list.size(); i++) {
  System.out.println((String)list.get(i));
}
```

〇コレクション

* List  
    * 重複ＯＫで順序付けされる  
        * ***ArrayList***  
        ⇒挿入と削除の処理が<span style="color: red;">線形的</span>に実施される。  
          `ランダムアクセス`はListにお任せ!!ただ、挿入と削除は低速で、同期サポートはしません!!
        * ***LinkedList***  
        ⇒ノード(要素)にデータ項目に加えて、次のノードに対する<span style="color : red;">ポインター</span>が格納されている。  
        `挿入と削除`はLinkdListにお任せだぁ!!
        * ***Vector***  
        ⇒ArrayListに<span style="color: red;">同期サポート</span>が追加されたもの。  
        `スレッドセーフ`なコレクションにしたければ、お任せあれ!!
* Set  
    * 重複NGで順不同で管理します。  
        * ***HashSet***  
        ⇒`データへのアクセス`はお任せだぁ!!ただ、データ項目の順序付けと同期サポートは出来ません...
        * ***TreeSet***  
        ⇒`ソート`したければお任せあれ!!ただ、アクセスは遅いです(´；ω；`)ｳｩｩ
        * ***LinkedHashSet***  
        ⇒HashSetに`2重リンクリスト`が追加されてるよ  
* Queue  
    * FIFO形式でデータ入力  
    ⇒`Deque`インターフェースも使える。(LIFO形式)  
* Map  
    * Keyは重複出来ないが、値は重複ＯＫ
        * ***HashMap***  
        ⇒`null`をKeyと値に使えます！
        * ***TreeMap***  
        ⇒`挿入`された順序で繰り返し順序が決まります。
        * ***LinkedHashMap***  
        ⇒`Keyを元にソート`をされて順序が決まります。

■イテレーター  
⇒コレクション内の要素を順番にアクセスしたい時に使えるよ。  

```java
Iterator<E> iter = hoge.iterator();

boolean hasNext(); //要素があるか判定
hoge.next(); // 要素を返す
```

〇ジェネリクス  

使える箇所は以下のみ  
・クラス定義及びインターフェース  

```java

//インターフェースにジェネリクスを利用する事で
interface Hoge<T> { void method(T t);}


//使い分けをしたい時に便利
Class Moji implements Hoge<String> {
  public void method(String s) { System.out.println("Hey");}
}

Class Number implements Hoge<Integer> {
  public void method(Integer s) { System.out.println(7);}
}

```

・メソッド  

```java
[修飾子] <型パラメータリスト> 戻り値の型 メソッド名 (データ型 引数) {}

public <T> T hoge(T value) { retrun value; }

```

・コンストラクタ  

他にも継承でも利用可能である。  

```java
//型パラメータでもデータ型でもOKと言う感じ
<型パラメータ extends データ型>
```
