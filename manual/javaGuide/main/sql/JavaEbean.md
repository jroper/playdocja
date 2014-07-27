<!-- translated -->
<!--
# Using the Ebean ORM
-->
# Ebean ORM を使う

<!--
## Configuring Ebean
-->
## Ebean の設定

<!--
Play 2.0 comes with the [[Ebean| http://www.avaje.org/]] ORM. To enable it, add the following line to `conf/application.conf`:
-->
Play 2.0 には [[Ebean| http://www.avaje.org/]] ORM が同梱されています。有効にするには以下の行を `conf/application.conf` に追加します。

```properties
ebean.default="models.*"
```

<!--
This defines a `default` Ebean server, using the `default` data source, which must be properly configured. You can actually create as many Ebean servers you need, and explicitly define the mapped class for each server.
-->
ここでは `default` データソースを使用する `default` Ebean サーバを定義していて、これは適切に設定する必要があります。 Ebean サーバは必要な分だけ作ることができ、また各サーバにマップされるクラスを明確に定義する事も出来ます。

```properties
ebean.orders="models.Order,models.OrderItem"
ebean.customers="models.Customer,models.Address"
```

<!--
In this example, we have access to two Ebean servers - each using its own database.
-->
この例では、2 つの Ebean サーバへのアクセスが設定されています。各サーバはそれぞれ異なるデータベースを使用しています。

<!--
Each `ebean.` config line (as above) can map *any* classes that Ebean may be interested in registering (eg. `@Entity`/`Model` classes, `@Embeddable`s, custom `ScalarType`s and `CompoundType`s, `BeanPersistController`s, `BeanPersistListener`s, `BeanFinder`s, `ServerConfigStartup`s, etc). These can be individually listed separated by commas, and/or you can use the wildcard `.*`. For example, `models.*` registers with Ebean all classes within the models package that Ebean can make use of.
-->
(上記した) それぞれの `ebean.` 設定行は、Ebean が登録することに関心を持つ *あらゆる* クラス (例えば `@Entity`/`Model` クラス, `@Embeddable`, カスタマイズした `ScalarType` と `CompoundType`, `BeanPersistController`, `BeanPersistListener`, `BeanFinder`, `ServerConfigStartup`, その他) とマッピングすることができます。

<!--
To customise the underlying Ebean Server configuration, you can either add a `conf/ebean.properties` file, or create an instance of the `ServerConfigStartup` interface to programmatically manipulate the Ebean `ServerConfig` before the server is initialised.
-->
根底にある Ebean サーバの設定をカスタマイズするために、`conf/ebean.properties` を追加するか、サーバが初期化される前に Ebean の `ServerConfig` をプログラムから操作するために `ServerConfigStartup` のインスタンスを作成することができます。

<!--
As an example, the fairly common problem of reducing the Sequence Batch Size in order to minimise sequence gaps, could be solved quite simply with a class like this:
-->
例えば、シーケンスのギャップを最小限にするためにシーケンスバッチサイズを減らすと言う、かなり一般的な問題は、以下のようなクラスによって、とてもシンプルに解決することができます。

```java
package models;

import com.avaje.ebean.config.ServerConfig;
import com.avaje.ebean.event.ServerConfigStartup;

public class MyServerConfigStartup implements ServerConfigStartup {
    @Override
    public void onStart(ServerConfig serverConfig) {
        serverConfig.setDatabaseSequenceBatchSize(1);
    }
}
```

<!--
Note that Ebean will also make use of a `conf/orm.xml` file (if present), to configure `<entity-mappings>`.
-->
Ebean は `<entity-mappings>` を設定するために (もし存在すれば) `conf/orm.xml` ファイルを活用することにも気を付けてください。

<!--
> For more information about Ebean, see the [[Ebean documentation | http://www.avaje.org/ebean/documentation.html]].
-->
> Ebean の詳細の情報については、 [[Ebean ドキュメント | http://www.avaje.org/ebean/documentation.html]] をご覧下さい。

<!--
## Using the play.db.ebean.Model superclass
-->
## play.db.ebean.Model のスーパークラスを使う

<!--
Play 2.0 defines a convenient superclass for your Ebean model classes. Here is a typical Ebean class, mapped in Play 2.0:
-->
Play 2.0 は Ebean モデルクラス向けの便利なスーパークラスを定義しています。Play 2.0 でマップされた典型的な Ebean クラスは以下のようになります。

```java
package models;

import java.util.*;
import javax.persistence.*;

import play.db.ebean.*;
import play.data.format.*;
import play.data.validation.*;

@Entity 
public class Task extends Model {

  @Id
  @Constraints.Min(10)
  public Long id;
  
  @Constraints.Required
  public String name;
  
  public boolean done;
  
  @Formats.DateTime(pattern="dd/MM/yyyy")
  public Date dueDate = new Date();
  
  public static Finder<Long,Task> find = new Finder<Long,Task>(
    Long.class, Task.class
  ); 

}
```
<!--
> Play has been designed to generate getter/setter automatically, to ensure compatibility with libraries that expect them to be available at **runtime** (ORM, Databinder, JSON Binder, etc). **If Play detects any user-written getter/setter in the Model, it will not generate getter/setter in order to avoid any conflict.**
-->
> **実行時** に getter/setter を必要とするライブラリ (ORM、データバインダ、JSON バインダ、等) との互換性を保つため、Play はこれらを自動生成するように設計されてきました。 **もしユーザが作成した getter/setter が Model で見つかった場合、Play は競合を避けるために getter/setter を生成しません。**
<!--
> **Caveats:**
-->
> **警告:**
<!--
> (1) Because Ebean class enhancement occurs *after* compilation, **do not expect Ebean-generated getter/setters to be available at compilation time.** If you'd prefer to code with them directly, either add the getter/setters explicitly yourself, or ensure that your model classes are compiled before the remainder of your project, eg. by putting them in a separate subproject.
-->
> (1) Ebean のクラス拡張はコンパイルの *後* に行われるので、**コンパイル時に Bbean が生成する getter/setter を利用できることを期待しないでください。** これらのメソッドを直接コーディングしたい場合は、getter/setter を自分で明示的に追加するか、例えばこれらを別のサブプロジェクトに配置するなどして、モデルクラスがプロジェクトの残りのクラスよりも前にコンパイルされることを保証してください。
<!--
> (2) Enhancement of direct Ebean field access (enabling lazy loading) is only applied to Java classes, not to Scala. Thus, direct field access from Scala source files (including standard Play 2 templates) does not invoke lazy loading, often resulting in empty (unpopulated) entity fields. To ensure the fields get populated, either (a) manually create getter/setters and call them instead, or (b) ensure the entity is fully populated *before* accessing the fields.
-->
> (2) Ebean の (遅延評価を可能にする) フィールドに直接アクセスする拡張は、 Java クラスにのみ適用され、Scala には適用されません。このため、(Play 2 の標準テンプレートを含む) Scala ソースファイルからのフィールド直接アクセスは遅延評価を実行せず、その結果、エンティティのフィールドは往々にして空 (未設定) になります。フィールドが設定されていることを保証するには、(a) getter/setter を手動で作成してフィールドアクセスの替わりに呼び出すか、(b) フィールドにアクセスする *前* にエンティティが完全に設定されていることを保証してください。

<!--
As you can see, we've added a `find` static field, defining a `Finder` for an entity of type `Task` with a `Long` identifier. This helper field is then used to simplify querying our model:
-->
ご覧のように、 `find` static フィールドが追加されました。これは識別子が `Long` である `Task` 型のエンティティに対する `Finder` を定義します。このヘルパーフィールドはモデルのクエリーを簡素化するために使われます。

```
// Find all tasks
List<Task> tasks = Task.find.all();
    
// Find a task by ID
Task anyTask = Task.find.byId(34L);

// Delete a task by ID
Task.find.ref(34L).delete();

// More complex task query
List<Task> tasks = find.where()
    .ilike("name", "%coco%")
    .orderBy("dueDate asc")
    .findPagingList(25)
    .getPage(1);
```

<!--
## Transactional actions
-->
## トランザクション管理されたアクション

<!--
By default Ebean will not use transactions. However, you can use any transaction helper provided by Ebean to create a transaction. For example:
-->
デフォルトでは Ebean はトランザクションを使用しません。しかし、Ebean から提供された全てのトランザクションヘルパーを使うことができます。例えば:

<!--
:: Rob Bygrave - 
The above statement is not correct. Ebean will use implicit transactions. To demarcate transactions you have 3 options: @Transactional, TxRunnable() or a beginTransaction(), commitTransaction()
-->
:: Rob Bygrave - 
上の記述は正確ではありません。 Ebean は暗黙的なトランザクションを使用します。トランザクションの境界を引く方法は 3 つあります。 @Transactional、TxRunnable() または beginTransaction() と commitTransaction() です。

<!--
See http://www.avaje.org/ebean/introtrans.html for examples and an explanation.
-->
例と解説は http://www.avaje.org/ebean/introtrans.html をご覧下さい。
:: - end note

```
// run in Transactional scope...  
Ebean.execute(new TxRunnable() {  
  public void run() {  
      
    // code running in "REQUIRED" transactional scope  
    // ... as "REQUIRED" is the default TxType  
    System.out.println(Ebean.currentTransaction());  
      
    // find stuff...  
    User user = Ebean.find(User.class, 1);  
    ...  
      
    // save and delete stuff...  
    Ebean.save(user);  
    Ebean.delete(order);  
    ...  
  }  
});
```

<!--
You can also annotate your action method with `@play.db.ebean.Transactional` to compose your action method with an `Action` that will automatically manage a transaction:
-->
アクションメソッドに `@play.db.ebean.Transactional` アノテーションを付けることで、メソッドにトランザクションを自動的に管理する `Action` を組み合わせる事も出来ます。

```
@Transactional
public static Result save() {
  ...
}
```

<!--
> **Next:** [[Integrating with JPA | JavaJPA]]
-->
> **次ページ:** [[JPA の統合 | JavaJPA]]