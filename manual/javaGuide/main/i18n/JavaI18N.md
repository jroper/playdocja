<!-- translated -->
<!--
# Externalising messages and internationalization
-->
# メッセージの外部ファイル化と多言語対応

<!--
## Specifying languages supported by your application
-->
## アプリケーションの対応言語を指定する

<!--
The specify your application’s languages, you need a valid language code, specified by a valid **ISO Language Code**, optionally followed by a valid **ISO Country Code**. For example, `fr` or `en-US`.
-->
アプリケーションの対応言語を指定するためには、まず正しい言語コードが必要です。言語コードの形式は、**ISO 言語コード**の後に省略可能な **ISO 国コード** を続ける、というものです。例えば、 `fr` や `en-US` は正しい言語コードです。

<!--
To start, you need to specify the languages that your application supports in its `conf/application.conf` file:
-->
次に、その言語コードを使って、 `conf/application.conf` ファイル内でアプリケーションの対応言語を指定しましょう。

```
application.langs=en,en-US,fr
```

<!--
## Externalizing messages
-->
## メッセージの外部ファイル化

<!--
You can externalize messages in the `conf/messages.xxx` files. 
-->
メッセージは `conf/messages.xxx` のようなファイルに外部化することができます。

<!--
The default `conf/messages` file matches all languages. You can specify additional language messages files, such as `conf/messages.fr` or `conf/messages.en-US`.
-->
デフォルトの `conf/messages` というファイルは、全ての言語にマッチします。このファイルに加えて `conf/messages.fr` や `conf/messages.en-US` のように言語ごとのメッセージファイルを指定することができます。

<!--
You can retrieve messages for the current language using the `play.i18n.Messages` object:
-->
現在の言語向けのメッセージは、`play.i18n.Messages`オブジェクトから取得することができます。

```
String title = Messages.get("home.title")
```

<!--
You can also specify the language explicitly:
-->
言語を明示的に指定することもできます。


```
String title = Messages.get(new Lang("fr"), "home.title")
```

<!--
> **Note:** If you have a `Request` in the scope, it will provide a default `Lang` value corresponding to the preferred language extracted from the `Accept-Language` header and matching one the application’s supported languages.
-->
> **ノート:** `Request` がスコープ内に存在する場合は、その `Accept-Language` ヘッダとアプリケーションの対応言語を考慮した上で適切な言語が決定されて、デフォルトの `Lang` 値として提供されます。

<!--
## Formatting messages
-->
## メッセージのフォーマット

<!--
Messages can be formatted using the `java.text.MessageFormat` library. For example, if you have defined a message like this:
-->
メッセージは `java.text.MessageFormat` ライブラリを使ってフォーマットされます。例えば、次のようなメッセージが定義されているとしましょう。

```
files.summary=The disk {1} contains {0} file(s).
```

<!--
You can then specify parameters as:
-->
このメッセージのパラメータは次のように指定できます。

```
Messages.get("files.summary", d.files.length, d.name)
```

<!--
## Retrieving supported languages from an HTTP request
-->
## HTTP リクエストから対応言語を取得する

<!--
You can retrieve a specific HTTP request’s supported languages:
-->
HTTP リクエストから対応言語を取得することができます。

```
public static Result index() {
  return ok(request().acceptLanguages());
}
```

<!--
> **Next:** [[The application Global object | JavaGlobal]]
-->
> **次ページ:** [[Global オブジェクト | JavaGlobal]]