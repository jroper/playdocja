<!-- translated -->
<!--
# Anorm, simple SQL data access
-->
# Anorm によるシンプルなデータアクセス

<!--
Play includes a simple data access layer called Anorm that uses plain SQL to interact with the database and provides an API to parse and transform the resulting datasets.
-->
Play には Anorm と呼ばれるシンプルなデータアクセスレイヤーが同梱されています。Anorm はデータベースとやり取りするのに SQL をそのまま利用すると同時に、結果のデータセットをパースしたり変換するための API を提供します。

<!--
**Anorm is Not an Object Relational Mapper**
-->
**Anorm は ORM (Object Relational Mapper) ではありません**

<!--
> In the following documentation, we will use the [[MySQL world sample database | http://dev.mysql.com/doc/index-other.html]]. 
--> 
> 以下のドキュメントでは、[[MySQL world サンプルデータベース | http://dev.mysql.com/doc/index-other.html]] を利用します。
> 
<!--
> If you want to enable it for your application, follow the MySQL website instructions, and configure it as  explained [[on the Scala database page | ScalaDatabase]].
-->
> もし、ご自分のアプリケーションでこのデータセットを利用されたい場合は、 MySQL ウェブサイトの手順の通りに従った後に、 [[Scala database のページ | ScalaDatabase]] で説明しているように設定してください。

<!--
## Overview
-->
## 概観

<!--
It can feel strange to return to plain old SQL to access an SQL database these days, especially for Java developers accustomed to using a high-level Object Relational Mapper like Hibernate to completely hide this aspect.
-->
Hibernate のような、SQL を完全に隠蔽するような高級な ORM に慣れた Java デベロッパーの方にとって、Anorm がデータベースアクセスに昔ながらの SQL を直接使っていることが奇妙に思われるかもしれません。

<!--
Although we agree that these tools are almost required in Java, we think that they are not needed at all when you have the power of a higher-level programming language like Scala. On the contrary, they will quickly become counter-productive.
-->
そのようなツールは、確かに Java では必要だったと思います。しかしながら，Scala のような高級な言語の恩恵を受けられる場合には全く必要ではないと考えます。実際のところ、そのような高級な ORM は Scala の場合はすぐ非生産的に感じられてしまうでしょう。

<!--
#### Using JDBC is a pain, but we provide a better API
-->
#### JDBC より良い API

<!--
We agree that using the JDBC API directly is tedious, particularly in Java. You have to deal with checked exceptions everywhere and iterate over and over around the ResultSet to transform this raw dataset into your own data structure.
-->
特に Java で JDBC API を直接使うことには飽き飽きされていると思います。Java で JDBC を直接使うような場合、コードのあらゆる箇所でチェック例外を考慮しなければならず、また生のデータセットをアプリケーション独自のデータ構造に変換するために ResultSet を何度も何度も読み込む必要があります。

<!--
We provide a simpler API for JDBC; using Scala you don’t need to bother with exceptions, and transforming data is really easy with a functional language. In fact, the goal of the Play Scala SQL access layer is to provide several APIs to effectively transform JDBC data into other Scala structures.
-->
そこで、私たちは JDBC よりシンプルな API を作ることにしました。Scala を利用すると、チェック例外に邪魔されることなく、また関数型言語の機能によりデータ構造の変換が本当に簡単になります。実際、Play の Scala SQL アクセスレイヤーが提供しているのは、JDBC のデータを Scala で定義されたデータ構造へ効率的に変換するための API です。

<!--
#### You don’t need another DSL to access relational databases
-->
#### リレーショナルデータベースへアクセスするために新しい DSL は必要ありません

<!--
SQL is already the best DSL for accessing relational databases. We don’t need to invent something new. Moreover the SQL syntax and features can differ from one database vendor to another. 
-->
SQL はそれ自体が既にリレーショナルデータベースへアクセスするために最適な DSL です。したがって、SQL に代わる何か新しいものを発明する必要はありません。また、SQL の文法や機能はデータベースベンダーによって異っています。

<!--
If you try to abstract this point with another proprietary SQL like DSL you will have to deal with several ‘dialects’ dedicated for each vendor (like Hibernate ones), and limit yourself by not using a particular database’s interesting features.
-->
このような SQL の方言をプロプライエタリかつ SQL ライクな DSL で抽象化しようとすると、(Hibernate のそれのように) ベンダ固有の `dialeects` を利用する必要が出てしまいます。これは同時に、データベースの特有の便利な機能を自ら制限することにもなってしまいます。

<!--
Play will sometimes provide you with pre-filled SQL statements, but the idea is not to hide the fact that we use SQL under the hood. Play just saves typing a bunch of characters for trivial queries, and you can always fall back to plain old SQL.
-->
Play には SQL ステートメントの組み立てを補助する機能もあります。しかし、その主な目的は SQL を隠蔽してしまうことではありません。Play は平凡なクエリについてタイプ量を削減してくれるだけであり、必要であればいつもどおりの生の SQL へフォールバックすることができます。

<!--
#### A type safe DSL to generate SQL is a mistake
-->
#### SQL を生成するための型安全な DSL は間違い

<!--
Some argue that a type safe DSL is better since all your queries are checked by the compiler. Unfortunately the compiler checks your queries based on a meta-model definition that you often write yourself by ‘mapping’ your data structure to the database schema. 
-->
型安全な DSL は、クエリがコンパイラによって検証されるという点において、ただの SQL より良いと言われることがあります。ただ残念なことに、このようなコンパイラのチェックは、あなたがデータ構造をデータベースのスキーマに人力でマッピングして作ったメタモデルに対するクエリに対して行われます。

<!--
There are no guarantees that this meta-model is correct. Even if the compiler says that your code and your queries are correctly typed, it can still miserably fail at runtime because of a mismatch in your actual database definition.
-->
このとき、定義したメタモデルが正しいという保証はありません。コンパイラがあなたのコードやクエリが正しく打ち込まれていると判断したとしても、メタモデルとデータベースのスキーマにミスマッチがあれば、残念なことに実行時エラーになってしまいます。

<!--
#### Take Control of your SQL code
-->
#### SQL コードをコントロールする

<!--
Object Relational Mapping works well for trivial cases, but when you have to deal with complex schemas or existing databases, you will spend most of your time fighting with your ORM to make it generate the SQL queries you want.
-->
Object Relational Mapping は一般的なデータモデルに関してはうまくいきます。しかし、実際にあなたが直面すると予想される複雑なスキーマや既存のデータベースに対しては、ORM を使って SQL クエリーを生成するのはかなりの負担になります。

<!--
Writing SQL queries yourself can be tedious for a simple ‘Hello World’ application, but for any real-life application, you will eventually save time and simplify your code by taking full control of your SQL code.
-->
SQL クエリを自分で書くのは `Hello World` アプリケーションのようなシンプルな場合には面倒ですが、実際のアプリケーションでは結果的に時間の節約やコードのシンプル化につながります。

<!--
## Executing SQL queries
-->
## SQL クエリを実行する

<!--
To start you need to learn how to execute SQL queries.
-->
まずは SQL の実行方法を知る必要があります。

<!--
First, import `anorm._`, and then simply use the `SQL` object to create queries. You need a `Connection` to run a query, and you can retrieve one from the `play.api.db.DB` helper:
-->
初めに、 `anorm._` をインポートして、 `SQL` オブジェクトを使ってクエリを作成しましょう。クエリを実行するためには `Connection` が必要で、その習得には `play.api.db.DB` ヘルパー関数が利用できます。

```scala
import anorm._ 
import play.api.db.DB

DB.withConnection { implicit c =>
  val result: Boolean = SQL("Select 1").execute()    
} 
```

<!--
The `execute()` method returns a Boolean value indicating whether the execution was successful.
-->
`execute()` メソッドは実行が成功したかどうかを表す Boolean 値を返します。

<!--
To execute an update, you can use `executeUpdate()`, which returns the number of rows updated.
-->
Update を実行するためには、`executeUpdate()` が利用できます。この関数はアップデートされた行数を返します。

```scala
val result: Int = SQL("delete from City where id = 99").executeUpdate()
```

<!--
If you are inserting data that has an auto-generated `Long` primary key, you can call `executeInsert()`. If you have more than one generated key, or it is not a Long, `executeInsert` can be passed a `ResultSetParser` to return the correct key.
-->
自動生成された `Long` 型の主キーを持つデータを Insert するのであれば、`executeInsert()` を呼び出すことができます。生成されるキーがひとつより多い場合や Long 型でない場合、正しいキーを返却する `ResultSetParser` を `executeInsert` に渡すことができます。

```scala
val id: Option[Long] = SQL("insert into City(name, country) values ({name}, {country})")
              .on("Cambridge", "New Zealand").executeInsert()
```
<!--
Since Scala supports multi-line strings, feel free to use them for complex SQL statements:
-->
Scala は複数行の文字列リテラルをサポートしているため、複雑な SQL ステートも気軽に書けます。

```scala
val sqlQuery = SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = 'FRA';
  """
)
```

<!--
If your SQL query needs dynamic parameters, you can declare placeholders like `{name}` in the query string, and later assign a value to them:
-->
もし SQL クエリが動的なパラメータをとるような場合、`{name}` のようなプレースホルダをクエリ文字列内に宣言して、後でそこに値を埋め込むことができます。

```scala
SQL(
  """
    select * from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = {countryCode};
  """
).on("countryCode" -> "FRA")
```

<!--
## Retrieving data using the Stream API
-->
## Stream API を利用してデータを取得する

<!--
The first way to access the results of a select query is to use the Stream API.
-->
select クエリから結果を参照する第一の方法は、 Stream API を利用することです。

<!--
When you call `apply()` on any SQL statement, you will receive a lazy `Stream` of `Row` instances, where each row can be seen as a dictionary:
-->
SQL 文に対して `apply()` を呼び出すと、`Row` インスタンスの `Stream` を、遅延評価される形で取得することができます。各行は辞書のような構造になっています。

```scala
// Create an SQL query
val selectCountries = SQL("Select * from Country")
 
// Transform the resulting Stream[Row] to a List[(String,String)]
val countries = selectCountries().map(row => 
  row[String]("code") -> row[String]("name")
).toList
```

<!--
In the following example we will count the number of `Country` entries in the database, so the result set will be a single row with a single column:
-->
次の例ではデータベース内の `Country` の個数を数えます。ResultSet は一カラム・一行になります。

```scala
// First retrieve the first row
val firstRow = SQL("Select count(*) as c from Country").apply().head
 
// Next get the content of the 'c' column as Long
val countryCount = firstRow[Long]("c")
```

<!--
## Using Pattern Matching
-->
## パターンマッチの利用

<!--
You can also use Pattern Matching to match and extract the `Row` content. In this case the column name doesn’t matter. Only the order and the type of the parameters is used to match.
-->
`Row` の内容を抽出するためにパターンマッチを利用することができます。このケースでは、カラム名は関係ありません。パラメータの順番と型だけがパターンマッチの際に考慮されます。

<!--
The following example transforms each row to the correct Scala type:
-->
次の例は各行を適切な Scala の型に変換します。

```scala
case class SmallCountry(name:String) 
case class BigCountry(name:String) 
case class France
 
val countries = SQL("Select name,population from Country")().collect {
  case Row("France", _) => France()
  case Row(name:String, pop:Int) if(pop > 1000000) => BigCountry(name)
  case Row(name:String, _) => SmallCountry(name)      
}
```

<!--
Note that since `collect(…)` ignores the cases where the partial function isn’t defined, it allows your code to safely ignore rows that you don’t expect.
-->
`collect(…)` は部分関数が定義されていない case を無視してくれるので、期待していない行を安全に読み飛ばすことができます。

<!--
## Special data types
-->
## 特別なデータ型

### Clobs

<!--
CLOBs/TEXTs can be extracted as so:
-->
CLOBs/TEXT は以下のようにして取り出すことができます:

```scala
SQL("Select name,summary from Country")().map {
  case Row(name: String, summary: java.sql.Clob) => name -> summary
}
```

<!--
Here we specifically chose to use `map`, as we want an exception if the row isn't in the format we expect.
-->
行が期待するフォーマットでない場合を例外としたいので、ここでは特に `map` を使用しています。

### Binary

<!--
Extracting binary data is similarly possible:
-->
同様にバイナリデータも取り出すことができます:

```scala
SQL("Select name,image from Country")().map {
  case Row(name: String, image: Array[Byte]) => name -> image
}
```

<!--
### Database interoperability
-->

### データベースの相互運用

<!--
Note that different databases will return different data types in the Row. For instance, an SQL 'smallint' is returned as a Short by org.h2.Driver and an Integer by org.postgresql.Driver. A solution to this is to simply write separate case statements for each database (i.e. one for development and one for production).
-->

異なるデータベースからは列内で異なるデータ型が返ることに注意してください。例えば、 SQL 'smallint' は org.h2.Driver は Short を返し、 org.postgresql.Driver は Integer を返します。この件は、単に各データベース(つまり開発用の一つおよび生産用の一つ)で別のステートメントを単に書くことで解決することができます。

<!--
## Dealing with Nullable columns
-->
## nullable なカラムを扱う

<!--
If a column can contain `Null` values in the database schema, you need to manipulate it as an `Option` type.
-->
カラムが データベーススキーマにおいて `Null` 値を含む場合、`Option` 型で操作する必要があります。

<!--
For example, the `indepYear` of the `Country` table is nullable, so you need to match it as `Option[Int]`:
-->
例えば、`Country` テーブルの `indepYear` というカラムが nullable の場合、それを `Option[Int]` にマッチさせます。

```scala
SQL("Select name,indepYear from Country")().collect {
  case Row(name:String, Some(year:Int)) => name -> year
}
```

<!--
If you try to match this column as `Int` it won’t be able to parse `Null` values. Suppose you try to retrieve the column content as `Int` directly from the dictionary:
-->
このカラムを `Int` としてマッチさせようとすると、`Null` 値をパースできません。辞書から カラムの内容を `Int` として取得する場合を考えてみましょう。

```scala
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Int]("indepYear")
}
```

<!--
This will produce an `UnexpectedNullableFound(COUNTRY.INDEPYEAR)` exception if it encounters a null value, so you need to map it properly to an `Option[Int]`, as:
-->
このコードは、null 値にヒットした場合に `UnexpectedNullableFound(COUNTRY.INDEPYEAR)` という例外を投げます。正しくは次のように `Option[Int]` にマップさせる必要があります。

```scala
SQL("Select name,indepYear from Country")().map { row =>
  row[String]("name") -> row[Option[Int]]("indepYear")
}
```

<!--
This is also true for the parser API, as we will see next.
-->
これは次で説明する parser API についても同様です。

<!--
## Using the Parser API
-->
## Parser API の利用

<!--
You can use the parser API to create generic and reusable parsers that can parse the result of any select query.
-->
様々な select 文の結果をパースする汎用的かつ再利用可能なパーサーを定義するためには parser API が利用できます。

<!--
> **Note:** This is really useful, since most queries in a web application will return similar data sets. For example, if you have defined a parser able to parse a `Country` from a result set, and another `Language` parser, you can then easily compose them to parse both Country and Language from a join query.
-->
> **Note:** Web アプリケーションのほとんどのクエリは同じようなデータセットを返すため、Parser API が効果的です。例えば、result set から `Country` をパースするパーサーと、`Language` をパースするパーサーを定義すると、それらを組み合わせ join クエリから Country と Language を同時にパースするパーサーを作成することができます。
>
<!--
> First you need to `import anorm.SqlParser._`
-->
> はじめに、`import anorm.SqlParser._` を書きます。

<!--
### Getting a single result
-->
### 結果を1行取得する

<!--
First you need a `RowParser`, i.e. a parser able to parse one row to a Scala value. For example we can define a parser to transform a single column result set row, to a Scala `Long`:
-->
まずは `RowParser` が必要です。これは、一行をパースして Scala の値へ変換するパーサーです。例えば、一カラムの result set の行をパースして Scala の `Long` 値を生成するようなパーサーは次のように記述します。

```scala
val rowParser = scalar[Long]
```

<!--
Then we have to transform it into a `ResultSetParser`. Here we will create a parser that parse a single row:
-->
次に、これを `ResultSetParser` へ変換する必要があります。次のように、一行をパースするパーサを定義しましょう。

```scala
val rsParser = scalar[Long].single
```

<!--
So this parser will parse a result set to return a `Long`. It is useful to parse to result produced by a simple SQL `select count` query:
-->
このパーサーは result set をパースして `Long` 値を返します。これは、`select count` のような単純な SQL 文の結果をパースするような場合に便利です。

```scala
val count: Long = SQL("select count(*) from Country").as(scalar[Long].single)
```

<!--
### Getting a single optional result
-->
### Option型の結果を1行取得する

<!--
Let's say you want to retrieve the country_id from the country name, but the query might return null. We'll use the singleOpt parser :
-->
coutry name から country_id を読み出そうとしたとします。しかし、クエリはnullを返すかもしれません。 singleOpt パーサーを使います。 :

```scala
val countryId: Option[Long] = SQL("select country_id from Country C where C.country='France'").as(scalar[Long].singleOpt)
```

<!--
### Getting a more complex result
-->
### より複雑な結果を取得する

<!--
Let’s write a more complicated parser:
-->
もっと複雑なパーサーを書いてみましょう。

<!--
`str("name") ~ int("population")`, will create a `RowParser` able to parse a row containing a String `name` column and an Integer `population` column. Then we can create a `ResultSetParser` that will parse as many rows of this kind as it can, using `*`: 
-->
`str("name") ~ int("population")` は 文字列型 `name` カラムと整数型 `population` カラムを含む列をパース可能な `RowParser` を作成します。また、 `*` を使うことで、この種の多くの行をパースする `ResultSetParser` を作成できます。

```scala
val populations:List[String~Int] = {
  SQL("select * from Country").as( str("name") ~ int("population") * ) 
}
```

<!--
As you see, this query’s result type is `List[String~Int]` - a list of country name and population items.
-->
ご覧のとおり、このクエリの結果の型は `List[String~Int]` 、つまり国名と人口のペアを要素とするリストになります。

<!--
You can also rewrite the same code as:
-->
このコードは次のように書くこともできます。

```scala
val result:List[String~Int] = {
  SQL("select * from Country").as(get[String]("name")~get[Int]("population")*) 
}
```

<!--
Now what about the `String~Int` type? This is an **Anorm** type that is not really convenient to use outside of your database access code. You would rather have a simple tuple `(String, Int)` instead. You can use the `map` function on a `RowParser` to transform its result to a more convenient type:
-->
さて、`String~Int` という型は一体何でしょうか？これは **Anorm** で定義されている型で、データベースアクセスに関するコード以外での利用には適していません。例えば、`(String, Int)` のような単純なタプルをパースしたいことが多いでしょう。そのためには、 `RowParser` の `map` 関数を使って、結果をもっと便利な型に変換します。

```scala
str("name") ~ int("population") map { case n~p => (n,p) }
```

<!--
> **Note:** We created a tuple `(String,Int)` here, but there is nothing stopping you from transforming the `RowParser` result to any other type, such as a custom case class.
-->
> **Note:** この例では `(String,Int)` というタプルを生成しましたが、`RowParser` の結果をもっと別の型に変換しても何ら問題ありません。例えば、何らかの case class に変換してもよいでしょう。

<!--
Now, because transforming `A~B~C` types to `(A,B,C)` is a common task, we provide a `flatten` function that does exactly that. So you finally write:
-->
さて、`A~B~C` を `(A,B,C)` に変換するというよくあるタスクを簡単にするために、 `flatten` という関数を用意しました。これを使うと、同じ例は次のように書けます。

```scala
val result:List[(String,Int)] = {
  SQL("select * from Country").as(
    str("name") ~ int("population") map(flatten) *
  ) 
}
```

<!--
### A more complicated example
-->
### より複雑な例

<!--
Now let’s try with a more complicated example. How to parse the result of the following query to retrieve the country name and all spoken languages for a country code?
-->
次はさらに複雑な例に挑戦してみましょう。次のクエリの結果から国名と、国コード別の言語を取得するにはどうしたらよいでしょうか?

```
select c.name, l.language from Country c 
    join CountryLanguage l on l.CountryCode = c.Code 
    where c.code = 'FRA'
```

<!--
Let’s start by parsing all rows as a `List[(String,String)]` (a list of name,language tuple):
-->
まず、全ての行から `List[(String,String)]` 、つまり国名と言語のタプルを要素とするリストをパースすることから始めます。

```scala
var p: ResultSetParser[List[(String,String)]] = {
  str("name") ~ str("language") map(flatten) *
}
```

<!--
Now we get this kind of result:
-->
結果は以下のようになります。

```scala
List(
  ("France", "Arabic"), 
  ("France", "French"), 
  ("France", "Italian"), 
  ("France", "Portuguese"), 
  ("France", "Spanish"), 
  ("France", "Turkish")
)
```

<!--
We can then use the Scala collection API, to transform it to the expected result:
-->
次に、この結果を変換して必要なデータを得るために、 Scala の Collection API を利用することができます。

```scala
case class SpokenLanguages(country:String, languages:Seq[String])

languages.headOption.map { f =>
  SpokenLanguages(f._1, languages.map(_._2))
}
```

<!--
Finally, we get this convenient function:
-->
最終的に、次のような便利な関数を作ることができます。

```scala
case class SpokenLanguages(country:String, languages:Seq[String])

def spokenLanguages(countryCode: String): Option[SpokenLanguages] = {
  val languages: List[(String, String)] = SQL(
    """
      select c.name, l.language from Country c 
      join CountryLanguage l on l.CountryCode = c.Code 
      where c.code = {code};
    """
  )
  .on("code" -> countryCode)
  .as(str("name") ~ str("language") map(flatten) *)

  languages.headOption.map { f =>
    SpokenLanguages(f._1, languages.map(_._2))
  }
}
```

<!--
To continue, let’s complicate our example to separate the official language from the others:
-->
続いて、もう少し複雑な例として、公式サポートされている言語をそれ以外の言語と分けてみましょう。

```scala
case class SpokenLanguages(
  country:String, 
  officialLanguage: Option[String], 
  otherLanguages:Seq[String]
)

def spokenLanguages(countryCode: String): Option[SpokenLanguages] = {
  val languages: List[(String, String, Boolean)] = SQL(
    """
      select * from Country c 
      join CountryLanguage l on l.CountryCode = c.Code 
      where c.code = {code};
    """
  )
  .on("code" -> countryCode)
  .as {
    str("name") ~ str("language") ~ str("isOfficial") map {
      case n~l~"T" => (n,l,true)
      case n~l~"F" => (n,l,false)
    } *
  }

  languages.headOption.map { f =>
    SpokenLanguages(
      f._1, 
      languages.find(_._3).map(_._2),
      languages.filterNot(_._3).map(_._2)
    )
  }
}
```

<!--
If you try this on the MySQL world sample database, you will get:
-->
これを MySQL での world サンプルデータベースに対して実行すると、次のような結果が得られます。

```
$ spokenLanguages("FRA")
> Some(
    SpokenLanguages(France,Some(French),List(
        Arabic, Italian, Portuguese, Spanish, Turkish
    ))
)
```

<!--
> **Next:** [[Integrating with other database access libraries | ScalaDatabaseOthers]]
-->
> **次ページ:** [[データベースアクセスライブラリの利用 | ScalaDatabaseOthers]]