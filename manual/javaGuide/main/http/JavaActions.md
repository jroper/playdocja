<!-- translated -->
<!--
# Actions, Controllers and Results
-->
# アクション、コントローラ、レスポンス

<!--
## What is an Action?
-->
## アクションとは?

<!--
Most of the requests received by a Play application are handled by an `Action`. 
-->
Play アプリケーションが受け取ったリクエストのほとんどは、`Action` によって処理されます。

<!--
An action is basically a Java method that processes the request parameters, and produces a result to be sent to the client.
-->
アクションは基本的にはリクエストを処理しクライアントへ送るレスポンスを生成する Java のメソッドです。

@[simple-action](code/javaguide/http/JavaActions.java)

<!--
An action returns a `play.mvc.Result` value, representing the HTTP response to send to the web client. In this example `ok` constructs a **200 OK** response containing a **text/plain** response body.
-->
アクションは `play.mvc.Result` 型の値を返し、これはクライアントへ送信される HTTP レスポンスを表しています。上記の例では、`ok` は コンテントタイプ **text/plain** のレスポンスボディを含む、ステータス **200 OK** のレスポンスを生成します。

<!--
## Controllers 
-->
## コントローラ

<!--
A controller is nothing more than a class extending `play.mvc.Controller` that groups several action methods.
-->
コントローラは複数のアクションメソッドをまとめて `play.mvc.Controller` を継承しただけのクラスです。

@[full-controller](code/javaguide/http/full/Application.java)

<!-- The simplest syntax for defining an action is a static method with no parameters that returns a `Result` value, as shown above. -->
アクションを定義する最も簡単な構文は、上記したように `Result` 型の型を返す引数無しの static メソッドを定義するというものです。

<!--
An action method can also have parameters:
-->
アクションメソッドは引数を取る事も出来ます。

@[params-action](code/javaguide/http/JavaActions.java)

<!--
These parameters will be resolved by the `Router` and will be filled with values from the request URL. The parameter values can be extracted from either the URL path or the URL query string.
-->
これらの引数は `Router` により解決され、リクエスト URL から値が入れられます。引数の値は URL のパスもしくは URL のクエリ文字列から抽出することができます。

<!--
## Results
-->
## Results

<!--
Let’s start with simple results: an HTTP result with a status code, a set of HTTP headers and a body to be sent to the web client.
-->
簡単な Result から始めてみましょう。ステータスコード、 HTTP ヘッダ、そしてボディがクライアントに送信されるというものです。

<!--
These results are defined by `play.mvc.Result`, and the `play.mvc.Results` class provides several helpers to produce standard HTTP results, such as the `ok` method we used in the previous section:
-->
これらの結果は `play.mvc.Result` で定義されていて、そして `play.mvc.Results` クラスは、前のセクションで使用した `ok` メソッドのような標準的な HTTP の結果を返すためのいくつかのヘルパーを提供します。

@[simple-result](code/javaguide/http/JavaActions.java)

<!--
Here are several examples that create various results:
-->
Result を作成する例をいくつかご紹介します。

@[other-results](code/javaguide/http/JavaActions.java)

<!--
All of these helpers can be found in the `play.mvc.Results` class.
-->
これらのヘルパーは全て `play.mvc.Results` クラスで定義されています。

<!--
## Redirects are simple results too
-->
## リダイレクトもただの Result です

<!--
Redirecting the browser to a new URL is just another kind of simple result. However, these result types don't have a response body.
-->
ブラウザを新しい URL へリダイレクトさせることも、ただの Result の一種です。違うのは、リダイレクトの Result はレスポンスボディを取らないということです。

<!--
There are several helpers available to create redirect results:
-->
リダイレクトを生成するヘルパーもいくつか用意されています。

@[redirect-action](code/javaguide/http/JavaActions.java)

<!--
The default is to use a `303 SEE_OTHER` response type, but you can also specify a more specific status code:
-->
デフォルトでは、 `303 SEE_OTHER` のステータスコードが使われていますが、別のステータスコードを指定する事もできます。

@[temporary-redirect-action](code/javaguide/http/JavaActions.java)

<!--
> **Next:** [[HTTP Routing | JavaRouting]]
-->
> **次ページ:** [[HTTP ルーティング | JavaRouting]]
