<!-- translated -->
<!--
# Play 2.0 for Scala developers
-->
# Scala 開発者のための Play 2.0

<!--
The Scala API for Play 2.0 application developers is available in the `play.api` package. 
-->
Play 2.0 アプリケーションは、`play.api` パッケージ内にある Scala API を使って開発することができます。

<!--
> The API available directly inside the `play` package (such as `play.mvc`) is reserved for Java developers. As a Scala developer, look at `play.api.mvc`.
-->
> (`play.mvc` のような) `play` パッケージ内の API は Java 開発者に予約されています。Scala 開発者は `play.api.mvc` などを参照してください。

<!--
## Main concepts
-->
## 主要なコンセプト

<!--
1. [[HTTP programming | ScalaActions]]
    1. [[Actions, Controllers and Results | ScalaActions]]
    1. [[HTTP routing | ScalaRouting]]
    1. [[Manipulating results | ScalaResults]]
    1. [[Session and Flash scopes | ScalaSessionFlash]]
    1. [[Body parsers | ScalaBodyParsers]]
    1. [[Actions composition | ScalaActionsComposition]]
1. [[Asynchronous HTTP programming | ScalaAsync]]
    1. [[Handling asynchronous results | ScalaAsync]]
    1. [[Streaming HTTP responses | ScalaStream]]
    1. [[Comet sockets | ScalaComet]]
    1. [[WebSockets | ScalaWebSockets]]
1. [[The template engine | ScalaTemplates]]
    1. [[Templates syntax | ScalaTemplates]]
    1. [[Common use cases | ScalaTemplateUseCases]]
1. [[HTTP form submission and validation | ScalaForms]]
    1. [[Form definitions | ScalaForms]]
    1. [[Using the form template helpers | ScalaFormHelpers]]
1. [[Working with Json | ScalaJson]]
    1. [[The Play Json library | ScalaJson]]
    1. [[Handling and serving Json requests | ScalaJsonRequests]]
    1. [[The Play Json Library with Generics | ScalaJsonGenerics]]
1. [[Working with XML | ScalaXmlRequests]]
    1. [[Handling and serving XML requests | ScalaXmlRequests]]
1. [[Handling file upload | ScalaFileUpload]]
    1. [[Direct upload and multipart/form-data | ScalaFileUpload]]
1. [[Accessing an SQL database | ScalaDatabase]]
    1. [[Configuring and using JDBC | ScalaDatabase]]
    1. [[Using Anorm to access your database | ScalaAnorm]]
    1. [[Integrating with other database access libraries | ScalaDatabaseOthers]]
1. [[Using the Cache | ScalaCache]]
    1. [[The Play cache API | ScalaCache]]
1. [[Calling WebServices | ScalaWS]]
    1. [[The Play WS API  | ScalaWS]]
    1. [[Connecting to OpenID services | ScalaOpenID]]
    1. [[Accessing resources protected by OAuth | ScalaOAuth]]
1. [[Integrating with Akka | ScalaAkka]]
    1. [[Setting up Actors and scheduling asynchronous tasks | ScalaAkka]]
1. [[Internationalization | ScalaI18N]]
    1. [[Messages externalisation and i18n | ScalaI18N]]
1. [[The application Global object | ScalaGlobal]]
    1. [[Application global settings | ScalaGlobal]]
    1. [[Intercepting requests | ScalaInterceptors]]
1. [[Testing your application | ScalaTest]]
    1. [[Writing tests | ScalaTest]]
    1. [[Writing functional tests | ScalaFunctionalTest]]
-->
1. [[HTTP プログラミング | ScalaActions]]
    1. [[アクション、コントローラ、レスポンス | ScalaActions]]
    1. [[HTTP ルーティング | ScalaRouting]]
    1. [[レスポンスの操作 | ScalaResults]]
    1. [[セッションとフラッシュスコープ | ScalaSessionFlash]]
    1. [[ボディパーサー | ScalaBodyParsers]]
    1. [[アクションの合成 | ScalaActionsComposition]]
1. [[非同期 HTTP プログラミング | ScalaAsync]]
    1. [[非同期レスポンスの処理 | ScalaAsync]]
    1. [[HTTP レスポンスのストリーミング | ScalaStream]]
    1. [[Comet | ScalaComet]]
    1. [[WebSocket | ScalaWebSockets]]
1. [[テンプレート・エンジン | ScalaTemplates]]
    1. [[テンプレートの文法 | ScalaTemplates]]
    1. [[よくある使い方 | ScalaTemplateUseCases]]
1. [[HTTP フォーム送信とバリデーション | ScalaForms]]
    1. [[フォームの定義 | ScalaForms]]
    1. [[フォームテンプレートヘルパの利用 | ScalaFormHelpers]]
1. [[JSON を使う | ScalaJson]]
    1. [[Play の JSON ライブラリ | ScalaJson]]
    1. [[Json リクエストの処理と送信 | ScalaJsonRequests]]
    1. [[ジェネリクスと Play Json ライブラリ | ScalaJsonGenerics]]
1. [[XML を使う | ScalaXmlRequests]]
    1. [[XML リクエストの処理と送信 | ScalaXmlRequests]]
1. [[ファイルアップロード処理 | ScalaFileUpload]]
    1. [[multipart/form-data のアップロード | ScalaFileUpload]]
1. [[SQL データベースアクセス | ScalaDatabase]]
    1. [[JDBC の設定と利用 | ScalaDatabase]]
    1. [[Anorm によるデータベースアクセス | ScalaAnorm]]
    1. [[データベースアクセスライブラリの利用 | ScalaDatabaseOthers]]
1. [[キャッシュを使う | ScalaCache]]
    1. [[Play の Cache API | ScalaCache]]
1. [[Web サービスの呼び出し | ScalaWS]]
    1. [[Play WS API | ScalaWS]]
    1. [[OpenID サーバへの接続 | ScalaOpenID]]
    1. [[OAuth によリ保護されたデータへのアクセス | ScalaOAuth]]
1. [[Akka との統合 | ScalaAkka]]
    1. [[アクターの設定と非同期タスクのスケジューリング | ScalaAkka]]
1. [[国際化 | ScalaI18N]]
    1. [[メッセージの外部化と i18n | ScalaI18N]]
1. [[アプリケーション Global オブジェクト | ScalaGlobal]]
    1. [[アプリケーションの全般設定 | ScalaGlobal]]
    1. [[リクエストのインターセプト | ScalaInterceptors]]
1. [[テストについて | ScalaTest]]
    1. [[テストの書き方 | ScalaTest]]
    1. [[機能テストの書き方 | ScalaFunctionalTest]]
    
<!--
## Advanced topics
-->
## 上級編

<!--
1. [[Handling data streams reactively | Iteratees]]
    1. [[Iteratees | Iteratees]]
    1. [[Enumerators | Enumerators]]
    1. [[Enumeratees | Enumeratees]]
-->
1. [[反応的なストリーム処理 | Iteratees]]
    1. [[Iteratee | Iteratees]]
    1. [[Enumerator | Enumerators]]
    1. [[Enumeratee | Enumeratees]]

<!--
## Tutorials
-->
## チュートリアル

<!--
1. [[Your first application | ScalaTodoList]]
-->
1. [[はじめてのアプリケーション | ScalaTodoList]]
