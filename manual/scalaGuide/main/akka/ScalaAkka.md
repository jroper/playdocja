<!-- translated -->
<!--
# Integrating with Akka
-->
# Akka の統合

<!--
[[Akka| http://akka.io/]] uses the Actor Model to raise the abstraction level and provide a better platform to build correct concurrent and scalable applications. For fault-tolerance it adopts the ‘Let it crash’ model, which has been used with great success in the telecoms industry to build applications that self-heal - systems that never stop. Actors also provide the abstraction for transparent distribution and the basis for truly scalable and fault-tolerant applications.
-->
[[Akka| http://akka.io/]] は、アクター・モデルに基づいて、これまでより高い抽象度で、並列かつスケーラブルなアプリケーションを実装するためのプラットフォームです。Akka は耐障害性を実現するために 'Let it crash' モデルを採用しています。これは、自己修復するアプリケーション、言い換えれば落ちないシステムを必要としていた通信業界で大きな成功を収めているモデルです。アクターは分散処理を意識せずに実現できるような抽象化を提供する一方で、スケーラブルかつ耐障害性のアプリケーションを実装するベースにもなります。

<!--
## The application actor system
-->
## アプリケーションのアクターシステム

<!--
Akka 2.0 can work with several containers called `ActorSystems`. An actor system manages the resources it is configured to use in order to run the actors which it contains. 
-->
Akka 2.0 は `アクターシステム` と呼ばれるいくつかのコンテナを持ちます。それぞれのアクターシステムは、それに含まれるアクターを動かすためのリソースを管理します。

<!--
A Play application defines a special actor system to be used by the application. This actor system follows the application life-cycle and restarts automatically when the application restarts.
-->
Play アプリケーションには、アプリケーション自身が使う特別なアクターシステムが定義されています。このアクターシステムはアプリケーションのライフサイクルに追従し、アプリケーションと共に自動的に再起動します。

<!--
> **Note:** Nothing prevents you from using another actor system from within a Play application. The provided default is convenient if you only need to start a few actors without bothering to set-up your own actor system.
-->
> **Note:** 独自のアクターシステムを利用しても全く問題ありません。デフォルトのアクターシステムは、実行するアクターの数が少なく、別のアクター・システムを自分で用意するまでもないような場合に利用するとよいでしょう。

<!--
You can access the default application actor system using the `play.api.libs.concurrent.Akka` helper:
-->
アプリケーションのデフォルトのアクターシステムを利用するためには、`play.api.libs.concurrent.Akka` ヘルパーを利用します。

```scala
val myActor = Akka.system.actorOf(Props[MyActor], name = "myactor")
```

<!--
## Configuration
-->
## 設定

<!--
The default actor system configuration is read from the Play application configuration file. For example, to configure the default dispatcher of the application actor system, add these lines to the `conf/application.conf` file:
-->
デフォルトのアクターシステムの設定は、Play アプリケーションの設定ファイルから読み込まれます。例えば、アプリケーションのアクターシステムのデフォルトディスパッチャを変更したい場合は、 `conf/application.conf` ファイルにその設定を数行追加します。

```
akka.default-dispatcher.fork-join-executor.pool-size-max =64
akka.actor.debug.receive = on
```

<!--
> **Note:** You can also configure any other actor system from the same file; just provide a top configuration key.
-->
> **Note:** Akka の規約に基づいて設定ファイルにトップの設定キーを記述することで、同じファイル内で全く別のアクターシステムを構成することもできます。

<!--
## Converting Akka `Future` to Play `Promise`
-->
## Akka `Future` から Play `Promise` への変換

<!--
When you interact asynchronously with an Akka actor we will get `Future` object. You can easily convert it to a Play `Promise` using the implicit conversion provided in `play.api.libs.concurrent._`:
-->
Akka アクターと非同期的にやり取りをすると、 `Future` オブジェクトが返ってきます。`play.api.libs.concurrent._` に用意されている implicit conversion を利用すると、この `Future` を Play の `Promise` オブジェクトに簡単に変換することができます。

```scala
def index = Action {
  Async {
    (myActor ? "hello").mapTo[String].asPromise.map { response =>
      Ok(response)      
    }    
  }
}
```

<!--
## Executing a block of code asynchronously
-->
## コードブロックを非同期的に実行する

<!--
A common use case within Akka is to have some computation performed concurrently, without needing the extra utility of an Actor. If you find yourself creating a pool of Actors for the sole reason of performing a calculation in parallel, there is an easier (and faster) way:
-->
Akka の典型的なユースケースは、Actor を特別難しい使い方をせずに、計算を並列化することです。例えば、あなたが並列計算を行うためにアクターのプールを作成しているようなら、もっと簡単（かつ高速な）方法があります。

```scala
def index = Action {
  Async {
    Akka.future { longComputation() }.map { result =>
      Ok("Got " + result)    
    }    
  }
}
```

<!--
## Scheduling asynchronous tasks
-->
## 非同期タスクのスケジューリング

<!--
You can schedule sending messages to actors and executing tasks (functions or `Runnable`). You will get a `Cancellable` back that you can call `cancel` on to cancel the execution of the scheduled operation.
-->
Akka では、アクターへのメッセージ送信やタスク(関数または `Runnable`)の実行を予約することができます。予約を行うと、結果として `Cancellable` のインスタンスが返ってきます。その `cancel` メソッドを呼び出すことで、予約した操作の実行をキャンセルすることができます。

<!--
For example, to send a message to the `testActor` every 30 minutes:
-->
例えば、`testActor` に 30 分おきにメッセージを送信する場合は次のように書きます。

```scala
Akka.system.scheduler.schedule(0 seconds, 30 minutes, testActor, "tick")
```

<!--
> **Note:** This example uses implicit conversions defined in `akka.util.duration` to convert numbers to `Duration` objects with various time units.
-->
> **Note:** この例では `akka.util.duration` に定義されている implicit conversion　を利用して、数値を時間単位の異なる `Duration` オブジェクトへ変換しています。

<!--
Similarly, to run a block of code ten seconds from now:
-->
同様に、コード・ブロックを今から 10 秒後に実行するには、次のように書きます。

```scala
Akka.system.scheduler.scheduleOnce(10 seconds) {
  file.delete()
}
```

<!--
> **Next:** [[Internationalization | ScalaI18N]]
-->
> **次ページ:** [[多言語対応 | ScalaI18N]]
