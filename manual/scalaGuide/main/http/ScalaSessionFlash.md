<!-- translated -->
<!--
# Session and Flash scopes
-->
# セッションとフラッシュスコープ

<!--
## How it is different in Play
-->
## Play におけるセッションとフラッシュスコープ

<!--
If you have to keep data across multiple HTTP requests, you can save them in the Session or Flash scopes. Data stored in the Session are available during the whole user Session, and data stored in the Flash scope are available to the next request only.
-->
複数のリクエストにまたがってデータを保持したい場合、セッションまたはフラッシュスコープにデータを保存することができます。一旦セッションに保存されたデータは、ユーザの 1 セッション中ずっと保持され、フラッシュスコープに保存されたデータは次のリクエストまで保持されます。

<!--
It’s important to understand that Session and Flash data are not stored by the server but are added to each subsequent HTTP request, using the cookie mechanism. This means that the data size is very limited (up to 4 KB) and that you can only store string values.
-->
セッションやフラッシュのデータはサーバに保存されるのではなく、クッキーを利用して後続のリクエストに追加されるということはとても重要です。このことは、データサイズがかなり制限される (4KB まで) と同時に、文字列型の値しか保存できないということを意味します。

<!--
Of course, cookie values are signed with a secret key so the client can’t modify the cookie data (or it will be invalidated).
-->
もちろん、クッキーの値は秘密鍵によって署名されているため、クライアントがクッキーのデータを変更することはできません (変更すると、値が無効化されます) 。

<!--
The Play Session is not intended to be used as a cache. If you need to cache some data related to a specific Session, you can use the Play built-in cache mechanism and use store a unique ID in the user Session to keep them related to a specific user.
-->
Play のセッションはキャッシュとして使われることを想定して作られてはいません。もし、特定のセッションに関するデータをキャッシュしたい場合、Play に組み込まれたキャッシュ機構を利用すると同時に、ユーザのセッションにユニーク ID を保存して、キャッシュを特定のユーザに対応づけることができます。

<!--
> There is no technical timeout for the Session. It expires when the user closes the web browser. If you need a functional timeout for a specific application, just store a timestamp into the user Session and use it however your application needs (e.g. for a maximum session duration, maximum inactivity duration, etc.).
-->
> セッションには技術的にタイムアウトが存在しません。セッションは、ユーザが web ブラウザを終了させたときに切れます。もし、特定のアプリケーションでタイムアウトの機能が必要になった場合は、ユーザのセッションにタイムスタンプを保存して、必要なところで参照すると良いでしょう (例えば、セッションの最長期間、期限切れまでに許される無操作時間等) 。

<!--
## Reading a Session value
-->
## セッション値の読み込み

<!--
You can retrieve the incoming Session from the HTTP request:
-->
次のように、送信されてきたセッションを HTTP リクエストから取り出すことができます。

@[index-retrieve-incoming-session](code/ScalaSessionFlash.scala)


<!--
Alternatively you can retrieve the Session implicitly from a request:
-->
別の方法として、リクエストから暗黙的にセッションを取り出すこともできます。

@[index-retrieve-incoming-session-implicitly](code/ScalaSessionFlash.scala)


<!--
## Storing data in the Session
-->
## セッションへの値の保存

<!--
As the Session is just a Cookie, it is also just an HTTP header. You can manipulate the session data the same way you manipulate other results properties:
-->
セッションはただのクッキーであると同時に、ただの HTTP ヘッダでもあります。したがって、Result の他のプロパティを操作するのと同様の方法で、セッションデータを操作することができます。

@[store-session](code/ScalaSessionFlash.scala)


<!--
Note that this will replace the whole session. If you need to add an element to an existing Session, just add an element to the incoming session, and specify that as new session:
-->
このコードはセッションを丸ごと入れ替えてしまうことに注意してください。もし、既存のセッションに要素を追加する必要がある場合、受けとったセッションに要素を追加したものを新たなセッションとして指定してください。

@[add-session](code/ScalaSessionFlash.scala)


<!--
You can remove any value from the incoming session the same way:
-->
同じ方法で、受け取ったセッション中の値を削除することができます。

@[remove-session](code/ScalaSessionFlash.scala)

<!--
## Discarding the whole session
-->
## セッションの破棄

<!--
There is special operation that discards the whole session:
-->
セッション全体を破棄するために、特別な操作が用意されています。

@[discarding-session](code/ScalaSessionFlash.scala)

<!--
## Flash scope
-->
## フラッシュスコープ

<!--
The Flash scope works exactly like the Session, but with two differences:
-->
フラッシュスコープは次の 2 点の違いを除いて、セッションと全く同じように動作します。

<!--
- data are kept for only one request
- the Flash cookie is not signed, making it possible for the user to modify it.
-->
- データは 1 リクエストまで保持されます
- フラッシュ向けのクッキーは署名されていないため、ユーザによって変更される可能性があります

<!--
> **Important:** The Flash scope should only be used to transport success/error messages on simple non-Ajax applications. As the data are just kept for the next request and because there are no guarantees to ensure the request order in a complex Web application, the Flash scope is subject to race conditions.
-->
> **重要:** フラッシュスコープはシンプルかつ非 Ajax なアプリケーションにおいて、成功/失敗メッセージをやり取りするためだけに利用すべきです。データは次のリクエストまでしか保持されず、また複雑な Web アプリケーションにおいてはリクエストの順序が保証できないため、フラッシュスコープが競合状態に陥る可能性があります。

<!--
Here are a few examples using the Flash scope:
-->
次にフラッシュスコープの利用例をいくつか示します。

@[using-flash](code/ScalaSessionFlash.scala)



<!--
To retrieve the Flash scope value in your view, just add an implicit with Flash:
-->
フラッシュスコープの値をビューから取得するには Flash に implicit を追加します:
```
@()(implicit flash: Flash)
...
@flash.get("success").getOrElse("Welcome!")
...
```

<!--
If the error '_could not find implicit value for parameter flash: play.api.mvc.Flash_' is raised then this is because your Action didn't import a request object. Add an "implicit request=>" as show below:
-->
アクションがリクエストオブジェクトを import しなかった場合、'_could not find implicit value for parameter flash: play.api.mvc.Flash_' というエラーが発生します。以下のように "implicit request=>" を追加します。

@[find-noflash](code/ScalaSessionFlash.scala)

<!--
> **Next:** [[Body parsers | ScalaBodyParsers]]
-->
> **次ページ:** [[ボディパーサー | ScalaBodyParsers]]