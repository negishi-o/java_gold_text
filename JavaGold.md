# Java


### クラスとインターフェース

-------------------

### ***〇インナークラス、匿名クラスの掟***

・ ***<span style="color: red">staticで修飾したインナークラスは、staticのみを参照すべし</span>***

```java

public class Sample {
  private String Msg = "Hey!";
  //↑↑staticクラスから取得したければ、ここをstaticに変える！！

  static class Call {
    public void getMsg() {
      System.out.println(Msg);
    }
  }
}

```

・ ***<span style="color: red">staticで修飾していないインナークラスで、staticの利用は控えるべし</span>***

```java

public class Sample {

  class Call {
    private static String Msg;
    //↑↑Callクラスがデフォルト修飾子なので、staticはNG！！！
    public void getMsg() {
      System.out.println(Msg);
    }
  }
}

```

・ ***<span style="color: red">匿名クラスでのコンストは禁ずる</span>***

```java

public class Sample {

  public static void main(String[] args) {
    Object obj = new Object() {

      //↓↓匿名クラスでのコンストはダメ！！
      public Object(String val) {
        System.out.println(val);
      }
    }
  }

}

```

### アノテーション

--------------

* @Deprecated
⇒非推奨のコンストラクタ、メソッド、変数などに付与する。<br>
コンパイル時に警告メッセージは表示される
* @SuppressWarnings
⇒引数に`unchecked`でList<String>の代わりにListなど型の使用に関する非推奨の利用をコンパイル時に警告なしで可能<br>
引数に`deperecation`で@Deprecatedで記載した箇所をコンパイル時に警告なしで可能に!! ***<span style="color: red">Deprecatedと2セットで使用と覚えよう！！</span>*** 
## 例外処理

--------------

### ***〇マルチキャスト***

・catch内で`catch(Exception | AException e)`と例外処理を複数キャッチも可能。

但し、キャッチ内に ***<span style="color: red">継承関係の例外はNG</span>*** ⇒`catch(IOException | FileNotFoundException e)`

他にも、`catch(Exception | AException e)`の受け取ったeはfinalになるので、再代入は禁止!!

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

### ***〇アサーション***

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

### ***〇そもそも、コレクションとジェネリクスとは何か?***

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

### ***〇コレクション***

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

■独自でソート順序を決めたい  
以下の2つが活躍出来る  

```java
java.lang.Comparable;
java.lang.Comparator;
```

***<span style="color: red;">Comparable</span>*** を利用する際の注意点  
⇒Comparableインターフェースを実装していないオブジェクトはNG  
　クラスの自然順序付けがメイン

実際の使い方は??  

```java
import java.util.*;

class Hoge {
    private String name;
    private Integer old;

    public Hoge(String name, Integer old) {
        this.name = name;
        this.old = old;
    }

    public String getName() {
        return this.name;
    }

    public Integer getOld() {
        return this.old;
    }
}

class MyNumberRule implements Comparator<Hoge> {
    public int compare(Hoge hoge1, Hoge hoge2) {
        return hoge1.getOld().compareTo(hoge2.getOld());
    }
}

public class Main {
public static void main(String[] args) {
    Hoge hoge1 = new Hoge("まさし", 10);
    Hoge hoge2 = new Hoge("たかしま", 20);
    Hoge hoge3 = new Hoge("かねこ", 50);
    Hoge hoge4 = new Hoge("ジョンソン", 70);

    ArrayList<Hoge> ary = new ArrayList<>();
    ary.add(hoge1);ary.add(hoge2);ary.add(hoge3);ary.add(hoge4);

    //定義した独自ソートクラスをインスタンス化して利用
    Collections.sort(ary, new MyNumberRule());
    for (Hoge obj : ary) {
        System.out.println(obj.getName() + " " + obj.getOld());
    }

    //こちらは直接、引数内で独自ソートを定義する技
    Collections.sort(ary, new Comparator<Hoge>() {
        public int compare(Hoge hoge1, Hoge hoge2) {
        return hoge1.getName().length() - hoge2.getName().length();
        }
    });

    for (Hoge obj : ary) {
        System.out.println(obj.getName() + " " + obj.getOld());
    }
}
}

```

↓↓↓実行結果↓↓↓↓

```java
まさし 10
たかしま 20
かねこ 50
ジョンソン 70
まさし 10
かねこ 50
たかしま 20
ジョンソン 70
```

### ***〇ジェネリクス***  

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

また、型パラメータはワイルドカード「`?`」も使用可能。  
何がパラメータ来るか分からない時は、ワイルドカードを使う！！  

## 関数型インターフェースとラムダ式

--------------

### ***〇関数型インターフェースとは何か***  
⇒定義されている`メソッドが1つだけのインターフェース`  

また、独自の関数型インターフェースも定義も出来たりする♪  
但し、以下の法則があるので利用時には注意が必要  

* 単一の抽象メソッドをもつインターフェースとするべし  
* staticとdefaultメソッドも使ってよし  
* java.lang.Objectクラスのpublicメソッドは抽象メソッドとして宣言してよし
* 明示する場合は`@Functionallnterface`を付与する事  

↓↓↓↓こうやって使う↓↓↓↓  

```java
@FunctionalInterface
interface MyHogeFuncInter<T> {
  void foo(T t); //これが単一の抽象メソッド
  String toString(); //これがObjectクラスのpublicメソッド
  static void X() {}; //これがstaticメソッド
  default void Y() {}; //これがdefaultメソッド
}
```

関数型インターフェースはこんなにもある!!  
覚えるのが大変だぁーーーーー(´；ω；`)ｳｩｩ  

* Functoin<T,R>  
  * R apply(T t)  
  ⇒引数として ***<span style="color:red">Ｔを受け取り、</span><span style="color:blue">結果とてRを返す</span>***  
* `Bi`Function<T,U,R>  
  * R apply(T t, U u)  
  ⇒引数とし ***<span style="color:red">ＴとU受け取り、</span><span style="color:blue">結果とてRを返す</span>***  
* Consumer<T`>  
  * void accept(T t)  
  ⇒引数として ***<span style="color:red">Tを受け取り終わり</span>***
* `Bi`Consumer<T,U>  
  * void accept(T t, U u)  
  ⇒引数として ***<span style="color:red">TとUを受け取り、終わり</span>***
* Predicate<T`>  
  * boolean test(T t)  
  ⇒引数として ***<span style="color:red">Tを受け取り、</span><span style="color:blue">判定結果を返す</span>***
* `Bi`Predicate<T,U>  
  * boolean test(T t, U u)  
  ⇒引数として ***<span style="color:red">TとUを受け取り、</span><span style="color:blue">判定結果を返す</span>***
* Supplier<T`>
  * T get()  
  ⇒ ***<span style="color:red">何も受け取りません!!</span><span style="color:blue">結果値してTを返す</span>***
* UnaryOperator<T`>  
  * T apply(T t)
  ⇒引数として ***<span style="color:red">Tを受け取り、Tを返す。</span>*** Fuction型を拡張したイメージだね!!
* `Bi`naryOperator<T`>  
  * T apply(T t1, T t2)
  ⇒引数として ***<span style="color:red">Tを2つ受け取り、Tを返す。</span>*** こっちはBiFuction型を拡張したイメージだぞ♪

とりあえず、`Bi`が付いたら引数は2つポイね。  
<span style="color:red">ラッパークラス</span>を使う必要があるね!!  
Consumerは引数を貰って、消費（Consumer）  
Supplierは求められたら、供給（Supplier）  

ジェネリクス型でないと関数型インターフェースで使えないと思った、  
そちらの方。。。なんと基本データ型でも利用が出来るのです!!  

■基本データ型での関数型インターフェース

対応の基本データ型： ***<span style="color:red;">int,double,long</span>***
×××にintを利用したければ、 ***Int*** として代入する感じ♪  

* ×××Function<R`>
    * R apply(××× value)  
* ×××Consumer
    * void accept(××× value)  
* ×××Predicate
    * boolean test(××× value)  
* ×××Supplier
    * int get`As×××`()
* ×××UnaryOperator
    * int apply`As×××`(××× operand)  
* ×××BiUnaryOperator
    * int apply`As×××`(××× left, ××× right)  

更に、引数はこの基本データ型で戻り値はこの基本データ型が良いとかを指定出来たりもする(;д;)   

* To×××Function<R`>
    * ××× apply`As×××`(R value)  
* ×××To□□□Function<R`>
    * □□□ apply`As□□□`(××× value)  

■関数型インターフェースの合成

なんと朗報で関数型インターフェースでAnd的な事も出来てしまう...
覚えるPointがいっぱいだぁーーーーーーーーーー(´_ゝ｀)

* andThen
⇒ ***<span style="color:red;">A実行した後に</span><span style="color:blue;">Bを実施</span>*** （A.andThen(B)）
* compose
⇒ ***<span style="color:blue;">B実行した後に</span><span style="color:red;">Aを実施</span>*** （A.compose(B)）
* and
⇒AとBを満たすか判定（A.and(B)）
* or
⇒AとBのどちらかを満たすか判定（A.or(B)）
* negate
⇒Aの判定をひっくり返す（A.negate()）

### ***〇ラムダ式***  

実際に使ってみる  

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
    //まずは匿名クラス
    String str1 = new Function<String, String>() {
      public String apply(String str) {
        return "Hello" + str;
      }
    }.apply("Naruto");
    System.out.println("匿名クラス" + str1);
    //ラムダ式（Step1）
    Function<String, String> f1 = (String str) -> {
      return "Hello" + str;
    };
    String str2 = f1.apply("Gokuuu");
    System.out.println("ラムダ式ステップ1" + str2);
    //ラムダ式（鬼省略）
    Function<String, String> f3 = str -> "Hello" + str;
    String str3 = f3.apply("Nezuko");
    System.out.println("ラムダ式鬼省略" + str3);
    }
}
```

↓↓↓↓出力結果↓↓↓↓

```java
匿名クラスHelloNaruto
ラムダ式ステップ1HelloGokuuu
ラムダ式鬼省略HelloNezuko
```

ここから複雑になるが、ラムダ式を使う上でメソッド参照が使われる  
メソッド参照は3種類あって、`staticメソッド参照、インスタンスメソッド参照、コンストラクタ参照`

↓↓↓↓使い方↓↓↓↓

```java
// *~*~~*~~*~まずはstaticメソッド参照*~*~~*~~*~
// ①戻り値のIntegerのメソッドを利用
Function<String, Integer> f = str -> Integer.perseInt(str);
// ②より↑をコンパクトにすると
Function<String, Integer> f = Integer::perseInt;

// ③はメソッド参照は利用しない場合
Comparator<Integer> comp = (x, y) -> Integer.compare(x, y);
// ④は↑をコンパクトにすると
Comparator<Integer> comp = Integer::compare;
System.out.println(comp.compare(1,2));

// *~*~~*~~*~インスタンスメソッド参照*~*~~*~~*~
// ⑤はインスタンスメソッド参照を利用しない場合
UnaryOperator<String> obj = s -> s.toUpperCase();
// これはＮＧ
UnaryOperator<String> obj = s.toUpperCase()　// ←sという謎変数がいきなり来てるから
// ⑥は⑤をコンパクトにすると
UnaryOperator<String> obj = String::toUpperCase;
System.out.println(obj.apply("takashi"));

// ⑦はインスタンスメソッド参照を利用しない場合
BiFunction<String, Integer, Character> ramu = (s, i) -> i.charAt(i);
// ⑧は↑をコンパクトにすると
BiFunction<String, Integer, Character> hoge = String::chartAt;
System.out.println(hoge.apply("html", 3));
//但し、これはＮＣ
BiFunction<Integer, String, Character> hogeNg = String::chartAt; //←第一引数と第二引数を受け取るので、第一引数が優先されるので判定出来ない

// *~*~~*~~*~コンストラクタ参照*~*~~*~~*~
public class Main {
  public static void main(String[]) {
    // ⑨はコンストラクタ参照を利用しない場合
    Supplier<Foo> obj = () -> new Foo();
    // ⑩は↑をコンパクトにすると
    Supplier<Foo> obj = Foo::new;

    // ⑪はコンストラクタ参照を利用しない場合
    Function<Integer, Foo> obj2 = i -> new Foo(i);
    // ⑫は↑をコンパクトにすると
    Function<Integer, Foo> obj2 = Foo::new;
    System.out.println(obj.apply(2).a);
  }
}
class Hoge {
  int a = 0;
  Hoge() { }
  Hoge(int a) { this.a = a;}
}
```

### ***〇ストリームAPI***  

* 中間操作
    * Stream<T'> filter(Predicate<? super T> predicate)
    ⇒ 指定された条件に一致するものを返す
    * Stream<T'> distinct()
    ⇒ 重複を除いた値を返す
    * Stream<T'> limit(long maxSize)
    ⇒ mazSize以内の要素数と言えば良いか分からないが、返す。
    * Stream<T'> skip(long n)
    ⇒ 先頭からn個までは取り除いた要素を返す
    * <R'> Stream<R'> map(Function<? super T, ? extends R> mapper)
    ⇒ ***<span style="color: red;">指定された関数を実行したものを返す</span>***
    * <R'> Stream<R'> `flat`Map(Function<? super T, ? extends Stream<? extends R>> mapper)
    ⇒ 指定された関数を実行した ***<span style="color: red;">複数の結果を纏めたもの</span>*** を返す
    * Stream<T'> sorted()
    ⇒ 自然順序に従ってソートした結果を返す
    * Stream<T'> sorted(`Comparator<? super T> comparator`)
    ⇒ ***<span style="color: red;">指定されたComparatorに従って</span>*** ソートした結果を返す
    * Stream<T'> peek(Consumer<? super T> action)
    ⇒ ストリーム内の値を確認した時にデバックコードで使うやつ!!

↓↓↓↓中間操作を使ってみた↓↓↓↓
```java
import java.util.*;
import java.util.stream.IntStream;
import java.util.stream.Stream;
import java.util.function.*;

public class Main {
    public static void main(String[] args) {
        //先頭がaから始まるものをソート
        Stream<String> stream1 = Stream.of("ami", "ao", "mia");
        stream1.filter(s -> s.startsWith("a"))
                .forEach(x -> System.out.print(x + " "));
        System.out.println();

        //重複を削除
        Stream<String> stream2 = Stream.of("ami", "ao", "ao");
        stream2.distinct()
                .forEach(x -> System
                        .out.print(x + " "));
        System.out.println();

        //limitで10まで作成
        IntStream.iterate(1, n -> n + 1)
                .limit(10L)
                .forEach(x -> System.out.print(x + " "));
        System.out.println();

        //skipで5までの数値をスキップ
        IntStream.rangeClosed(1, 10)
                .skip(5L)
                .forEach(x -> System.out.print(x + " "));
        System.out.println();

        //mapでStringからStringの操作
        Stream<String> stream1a = Stream.of("naoki", "aiko", "ami");
        Stream<String> stream1b = stream1a.map(s -> s.toUpperCase());
        stream1b.forEach(x -> System.out.print(x + " "));
        System.out.println();

        //mapでStringからInt型への操作
        Stream<String> stream2a = Stream.of("naoki", "aiko", "ami");
        Stream<Integer> stream2b = stream2a.map(s -> s.length());
        stream2b.forEach(x -> System.out.print(x + " "));
        System.out.println();

        //flatMapで1要素に対して複数の結果を操作
        List<Integer> data1 = Arrays.asList(10);
        List<Integer> data2 = Arrays.asList(50,80);
        List<Integer> data3 = Arrays.asList(100, 300, 500);

        List<List<Integer>> dataList = 
                Arrays.asList(data1, data2, data3);
        dataList.stream()
                .flatMap(data -> data.stream())
                .forEach(x -> System.out.print(x + " "));
        System.out.println();

        //ソートを使うけど、Comparatorオブジェクトを使うVer.
        Stream.of("naoki", "aiko", "ami")
                .sorted(Comparator.reverseOrder())
                .forEach(x -> System.out.print(x + " "));
        System.out.println();

    }
}

```

↓↓↓↓実行結果
```java
$ java ./src/04java/Main.java
ami ao 
ami ao
1 2 3 4 5 6 7 8 9 10 
6 7 8 9 10
NAOKI AIKO AMI 
5 4 3
10 50 80 100 300 500 
naoki ami aiko 
```

* 終端操作
    * boolean `all`Match(Predicate<? super T> predicate)
    ⇒ ***<span style="color: red;">全ての要素</span>*** が条件に一致するか判定
    * boolean `any`Match(Predicate<? super T> predicate)
    ⇒ ***<span style="color: red;">いずれかの要素</span>*** が条件に一致するか判定
    * boolean `none`Match(Predicate<? super T> predicate)
    ⇒ ***<span style="color: red;">どの要素も</span>*** が条件に一致していないか判定
    * long count()
    ⇒数を返す
    * Optional<T`> find`Any`()
    ⇒ ***<span style="color: red;">いずれかの要素</span>*** を返す
    * Optional<T`> find`First`()
    ⇒ ***<span style="color: red;">最初の要素</span>*** を返す
    * void forEach(Consumer<? super T> action)
    ⇒ 各要素にactionを実行
    * Optional<T`> min(Consumer<? super T> comparator)
    ⇒最小の要素を返す
    * Optional<T`> max(Consumer<? super T> comparator)
    ⇒最大の要素を返す
    * T reduce(T identity, BinaryOperator<T> accumulator)
    ⇒ ***<span style="color: red;">要素を累積して</span>*** 返す
       * `Optional`<T'> reduce(BinaryOperator<T'> accumulator)
       ⇒ ***<span style="color: blue;">戻り値がOptional</span>*** を返すものもある
    * Object[] toArray()
    ⇒ 要素を含む配列を返す

■ Optionalクラスとは??
1つの値を保存しているクラス。。。クラスにnullで処理を進めた時にいざ、getして値を取り出した際に  
ヌルポが発生してわぁーーーてなるが、Optionalクラスを使えばnullかどうか判定して値をどう処理するか可能になる。

↓↓↓↓メソッド一覧(出てきそうなものだけ抜粋)↓↓↓↓
* boolean isPresent()
⇒値が存在するかチェック出来る
* void `if`Present(Consumer<? super T> consumer)
⇒ ***<span style="color: red;">値があれば、値を出力して無ければ何もしない</span>*** 
* T orElse(T other)
⇒値があれば返します
* T orElseGet(Supplier<? extends T> other)
⇒値があれば、処理をして返します

また、基本データ型でもOptionalクラスは利用出来て、渡されるストリームによって終端操作の利用が使い方が分岐される。。。

* Streamで来た時
  ⇒ 戻り値はOptional<T'>で行き
  終端操作は`findAny,findFirst,max,min`が利用出来る。
* IntStreamで来た時
  ⇒ 戻り値はOptional ***<span style="color: red;">Double</span>*** で行く場合は
  終端操作は`avarage`が利用出来る。
  ⇒ 戻り値はOptional ***<span style="color: red;">Int</span>*** で行く場合は
  終端操作は`findAny,findFirst,max,min`が利用出来る。
  ⇒ 戻り値は ***<span style="color: blue;">int</span>*** で行く場合は
  終端操作は`sum`が利用出来る。

↓↓↓↓終端操作を使ってみた↓↓↓↓

```java

public class Main { 
    public static void main(String[] args) {
        List<String> data = Arrays.asList("kimura","ai","taka");
        //全て要素に4文字以上で存在するか確認
        boolean result1 = data.stream().allMatch(s -> s.length() >= 4);
        //いずれかの要素に4文字が存在するか確認
        boolean result2 = data.stream().anyMatch(s -> s.length() == 4);
        //全ての要素が2文字以下ではない事を確認
        boolean result3 = data.stream().noneMatch(s -> s.length() == 2);

        System.out.println(result1);
        System.out.println(result2);
        System.out.println(result3);

        System.out.println("~*~*~*~*~*~*forEach()~*~*~*~*");
        // 要素の展開と要素数を確認
        data.stream().forEach(System.out::println);
        System.out.println(data.stream().count());

        System.out.println("~*~*~*~*~*~*reduce()~*~*~*~*");
        // 要素を全て合算する
        Stream<Integer> stream = Stream.of(45, 5, 50);
        //合算する際の初期値
        int start = 0;
        int result4 = stream.reduce(start, (a, b) -> a + b);
        System.out.println(result4);

        //こっちは同じreduceメソッドだけど、リターン値が別ver.
        Stream<Integer> stream2 = Stream.of(45, 5, 50);
        BinaryOperator<Integer> operator = (a, b) -> a + b;
        Optional<Integer> result5 = stream2.reduce(operator);
        result5.ifPresent(System.out::println);

        System.out.println("~*~*~*~*~*~*max()~*~*~*~*");
        List<Integer> data2 = Arrays.asList(89, 56, 77);
        //max内にComparatorクラスのstaticメソッドが使うので注意!!
        Optional<Integer> result6 = 
                data2.stream().max(Comparator.naturalOrder());
        result6.ifPresent(System.out::println);


    }
}

```

↓↓↓↓コンパイル結果↓↓↓↓

```java
$ java src/01/Main.java 
false
true
false
~*~*~*~*~*~*forEach()~*~*~*~*
kimura
ai
taka
3
~*~*~*~*~*~*reduce()~*~*~*~*
100
100
~*~*~*~*~*~*max()~*~*~*~*
89
```

### ***〇モジュールシステム***  

■コマンドリストはこちら♪

|コマンド|内容|
|----|----|
|-d|クラスファイルの生成場所を指定|
|-p(--module-path)|モジュールが格納されている場所を指定|
|-m(-module)|ルートモジュールを指定(main含むクラスがある所)|
|-list-modules|参照可能なモジュールを出力|
|-d(-describe-module)|モジュール記述子の情報を出力|
|--show-module-resolution|モジュールの解決の様子を出力|

■こっちはjlinkコマンドリスト
※カスタムコマンドが生成出来るようになる便利もの

|コマンド|内容|
|----|----|
|--add-modules|イメージに追加するモジュールを指定|
|-c(--compress)|リソースの圧縮を有効化<br>0:圧縮なし<br>1:定数文字の共有<br>2:ZIP|
|--launcher command=module|モジュールのランチャー・コマンド名<br>`commandに自分が指定したいのに置き換えて使う感じ`|
|-output|出力ディレクトリ|

■こちらはmodule-info.javaに使う定義リスト

* requires <必要なモジュール名>
* requires transitive <間接エクスポートするモジュール名>
* exports <公開するモジュール名>
* provides <サービス> with <サービスプロバイダ>
⇒インターフェースとか抽象クラスを<サービス>に記載するイメージ
* use <サービス>
⇒providesで指定した<サービス>を指定するイメージ!!

### ***〇並列処理***  

■スレッドとは何ですか?
処理の最小単位であり、このスレッドが複数存在し、並列処理で実現するのがマルチスレッドです( ..)φメモメモ

実際に使う際は、`Thread`と`Runnable`がまずは基本。
スレッドが終了したものを再度、呼び出すと ***<span style="color: blue;">llegaThreadStateException</span>*** が発生するので使う時注意が必要だ!!

```java
public class Main {
    public static void main(String[] args) {
        //こっちはThreadを利用した時の記述
        ThreadA a = new ThreadA();
        a.start();

        //Runableはコンパクトに記述が出来る
        new Thread(
            () -> {
                System.out.println("Hey!!");
            }
        ).start();
    }
}

class ThreadA extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("A:" + i);
        }
    }
}

```

↓↓↓↓実行結果↓↓↓↓

```java
$ java 04/Main.java 
Hey!!
A:0
A:1
A:2
A:3
A:4
```

■スレッドを制御
Threadクラスにはいくつかメソッドがあるので、一覧はこちらは♪♪

* static void sleep(long millis) throws InterruptedException
⇒呼び出しのスレッドを指定時間、停止可能
* final void  ***<span style="color: blue;">join</span>*** () throws InterruptedException
⇒実行中のスレッドが終了するまで ***<span style="color: blue;">待機</span>*** 
* static void ***<span style="color: blue;">yield</span>*** ()
⇒実行しているスレッドを一時的に止めて、 ***<span style="color: blue;">他のスレッドを実行</span>*** 
* void ***<span style="color: red;">interrupt</span>*** ()
⇒ ***<span style="color: red;">休止中のスレッドに割り込みを入れる。</span>***

```java

public class Main {
    public static void main(String[] args) {
        Thread threadA  = new Thread(() -> {
            System.out.println("ThreadA : sleep Start");
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                System.out.println("ThreadA : reborn");
            }
            System.out.println("ThreadA : 再開しました");
        });
        threadA.start();

        try {
            System.out.println("main : sleep Start");
            Thread.sleep(2000);
            System.out.println("main : sleep end");
            //割り込みをここで入れる!!
            threadA.interrupt();
        } catch (InterruptedException e) {
            System.out.println("main : 割り込みをしました");
        }
    }
}

```

↓↓↓↓実行結果↓↓↓↓

```java
$ java -Dfile.encoding=utf-8 05/Main.java 
main : sleep Start
ThreadA : sleep Start
main : sleep end
ThreadA : reborn
ThreadA : 再開しました
```

ただ、これだけでは排他制御と同期制御が出来ないので、
ここで ***<span style="color: red;">synchronized</span>*** を対象の処理に追加することで
追加した個所はロック状態を保有が出来る。ただ、これは排他制御の機能を付与するだけなので、
合わせて同期制御も利用すること。

■同期処理のメソッドはこちら！！

* final void wait() throws `InterruptedException`
⇒現在のスレッドを待機
* final void wait(logn timeout) throws `InterruptedException`
⇒指定された時間まで、現在のスレッドを待機
* final void ***<span style="color: red;">notify</span>*** ()
⇒待機中のスレッドを ***<span style="color: red;">1つ再開</span>*** させる
* final void ***<span style="color: red;">notifyAll</span>*** ()
⇒待機中のスレッドを ***<span style="color: red;">全て再開</span>*** させる

```java
public class Main {
    public static void main(String[] args) {
        Share share = new Share();
        ThreadA threadA = new ThreadA(share);
        ThreadB threadB = new ThreadB(share);
        threadA.start();
        threadB.start();
    }
}

    class Share {
        private int a = 0;
        private String b;

        public synchronized void set() {
            while (a != 0) {
                try {
                    //一旦止める
                    wait();
                } catch (InterruptedException e) {
                }
            }
            //止まっているprintメソッドを動かす
            notify();
            a++;
            b = "Hello";
            System.out.println("set() a:" + a + " b:" + b);
        }

        public synchronized void print() {
            while (b == null) {
                try {
                    //一旦止める
                    wait();
                } catch (InterruptedException e) {
                }
            }
            //止まっているAを動かす
            notify();
            a--;
            b = null;
            System.out.println("print() a:" + a + " b:" + b);
        }

    }
    
    class ThreadA extends Thread {
        private Share share;

        public ThreadA(Share share) {
            this.share = share;
        }

        public void run() {
            for (int i = 0; i < 10; i++) {
                share.set();
            }
        }
    }
    
    class ThreadB extends Thread {
        private Share share;

        public ThreadB(Share share) {
            this.share = share;
        }

        public void run() {
            for(int i =0; i < 10; i++) {
                share.print();
            }
        }
    }

```

↓↓↓↓動かした結果↓↓↓↓
※交互にスレッド処理が行われているー

```java
$ java 06/Main.java
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello 
print() a:0 b:null
set() a:1 b:Hello 
print() a:0 b:null
set() a:1 b:Hello 
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
set() a:1 b:Hello
print() a:0 b:null
```

### ***〇Atomicクラス~原子性を強力に込めて~***  

`synchronized`を使った排他制御が強力であり、パフォーマンスを下がる場合もあるので、原子性が担保されたAtomicパッケージが使われる。

使い方はこんな感じ

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {
        Valu val = new AtomicValu();

        ExecutorService exec = Executors.newFixedThreadPool(2);
        exec.submit(new Task(val));
        exec.submit(new Task(val));

        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

        System.out.println(val.get());
        exec.shutdown();
    }
}
```

```java
public class Valu {
    private int num = 0;

    public void add(int num) {
        this.num = num;
    }

    public int get() {
        return this.num;
    }
}

```

```java
public class Task implements Runnable {
    private Valu val;

    public Task(Valu val) {
        this.val = val;
    }

    @Override
    public void run() {
        this.add(100);
    }

    private void add(int num) {
        this.val.add(num);
    }
}

```

↓↓↓↓アトミック利用↓↓↓↓

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicValu extends Valu{
    private AtomicInteger num = new AtomicInteger(0);
    //↑↑今回はIntegerがAtomic〇〇に指定

    public void add(int num) {
        this.num.addAndGet(num);
        //↑↑これで前のスレッド処理が終わったら、加算されるようになる
        
    }

    public int get() {
        return this.num.intValue();
    }
}

```


### ***〇ローカライズ***  

```java
import java.util.*;
import java.io.*;

public class Main {

    public static void main(String[] args) throws Exception {
        //コンストで言語、国、派生の順で指定するパターン
        Locale locale = new Locale("ja", "JP", "JP");
        System.out.println("国名" + locale.getCountry());
        System.out.println("言語" + locale.getLanguage());

        //定数で指定するパターン
        Locale locale2 = Locale.US;
        System.out.println("国名" + locale2.getCountry());
        System.out.println("言語" + locale2.getLanguage());

        System.out.println("IETE言語" + locale.toLanguageTag());
    
        Locale locale3 = new Locale.Builder()
                .setLanguage("jp")
                .setRegion("JP")
                .setScript("Japn")
                .build();
        System.out.println("国名" + locale3.getCountry());
        System.out.println("言語" + locale3.getLanguage());

        //プロパティファイルを取得
        Properties prop = new Properties();
        prop.load(new FileReader("sample.properties"));

        //一覧表示の仕方（part1）
        Set keys = prop.keySet();
        for (Object key : keys) {
            System.out.println(prop.get(key));
        }

        //一覧表示の仕方（part2）
        System.out.println("----------------------");
        prop.forEach((k, v) -> System.out.println(k + "=" + v));
        
        //一覧表示の仕方（part3）
        System.out.println("----------------------");
        prop.list(System.out);

        //対象keyだけの表示
        System.out.println("----------------------");
        System.out.println(prop.getProperty("name"));


        //環境の言語に沿って、プロパティファイルの取得方法
        //第二引数に言語指定で、変更可能
        System.out.println("----------------------");
        ResourceBundle resource = ResourceBundle.getBundle("sample", Locale.JAPAN);
        System.out.println(resource.getString("bye"));
    }
    
}

```
