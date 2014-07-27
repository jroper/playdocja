<!-- translated -->
<!--
# The Play cache API
-->
# キャッシュ API

<!--
Caching data is a typical optimization in modern applications, and so Play provides a global cache. An important point about the cache is that it behaves just like a cache should: the data you just stored may just go missing.
-->
データのキャッシュは現代的なアプリケーションではよく使われるパフォーマンスの最適化手法です。Play は、アプリケーション全体で利用できるグローバルなキャッシュを提供しています。キャッシュの重要な点は、通常のキャッシュとして期待されている通りに振る舞うということです。つまり、たった今保存したデータが、いつの間にか消えているということがありえます。

<!--
For any data stored in the cache, a regeneration strategy needs to be put in place in case the data goes missing. This philosophy is one of the fundamentals behind Play, and is different from Java EE, where the session is expected to retain values throughout its lifetime. 
-->
キャッシュに保存されているデータは、いつでも消去される可能性があります。その時に備えて、データを再生成する方法は常に用意しておく必要があります。この哲学は Play の基盤の一つであり、セッションがその有効期間の間ずっと値を保持するという Java EE の哲学とは異なります。

<!--
The default implementation of the cache API uses [[EHCache| http://www.ehcache.org/]] and it's enabled by default. You can also provide your own implementation via a plugin.
-->
キャッシュ API のデフォルト実装には [[EHCache| http://www.ehcache.org/]] を利用しており、これはデフォルトで有効になっています。別の実装をプラグインとして提供することもできます。 


<!--
## Accessing the Cache API
-->
## キャッシュ API へのアクセス

<!--
The cache API is provided by the `play.cache.Cache` object. This requires a cache plugin to be registered.
-->
キャッシュ API は `play.cache.Cache` オブジェクトにより提供されています。このオブジェクトを利用するには、キャッシュプラグインの登録が必要です。

<!--
> **Note:** The API is intentionally minimal to allow various implementations to be plugged in. If you need a more specific API, use the one provided by your Cache plugin.
-->
> **注意:** この API は様々な実装をプラグインできるよう、意図的に最小限に抑えられています。より特殊な API が必要な場合、別のキャッシュプラグインにより提供される API を使用してください。

<!--
Using this simple API you can store data in the cache:
-->
このシンプルな API を使ってキャッシュにデータ格納することができます:

```
Cache.set("item.key", frontPageNews);
```

<!--
Optionally you can specify a expiration (in seconds) for the cache:
-->
オプションとして、キャッシュの有効期間を (秒単位で) 指定することができます。

```
// Cache for 15 minutes
Cache.set("item.key", frontPageNews, 60 * 15);
```

<!--
You can retrieve the data later:
-->
このデータを取得するためには次のようにします。

```
News news = Cache.get("item.key");
```

<!--
To remove an item from the cache use the `remove` method:
-->
キャッシュから要素を削除するときは `remove` メソッドを使います:

```
Cache.remove("item.key")
```


<!--
## Caching HTTP responses
-->
## HTTP レスポンスのキャッシュ

<!--
You can easily create a smart cached action using standard `Action` composition. 
-->
標準的な `Action` の合成を使って、レスポンスをキャッシュするアクションを簡単に作成することができます。

<!--
> **Note:** Play HTTP `Result` instances are safe to cache and reuse later.
-->
> **注意:** Play の HTTP `Result` インスタンスは、安全にキャッシュし、あとから再利用することができます。

<!--
Play provides a default built-in helper for the standard case:
-->
Play は標準的に利用できるデフォルトの組み込みヘルパを提供しています。

```
@Cached("homePage")
public static Result index() {
  return ok("Hello world");
}
```

<!--
## Caching in templates  
-->
## テンプレートにおけるキャッシュ

<!--
You may also access the cache from a view template.
-->
ビューテンプレートからキャッシュにアクセスすることもできます。

```
@cache.Cache.getOrElse("cached-content", 3600) {
     <div>I’m cached for an hour</div>
}
```

<!--
## Session cache
-->
## セッションキャッシュ

<!--
Play provides a global cache, whose data are visible to anybody. How would one restrict visibility to a given user? For instance you may want to cache metrics that only apply to a given user.
-->
Play はアプリケーション内であればどこからでもデータを参照することができるグローバルなキャッシュを提供しています。データを特定のユーザのみ参照できるように制限をかけるにはどうしたらいいでしょうか？例えば、特定のユーザにのみ適用される数値指標をキャッシュしたい場合は次のようにします。


```
// Generate a unique ID
String uuid=session("uuid");
if(uuid==null) {
	uuid=java.util.UUID.randomUUID().toString();
	session("uuid", uuid);
}

// Access the cache
News userNews = Cache.get(uuid+"item.key");
if(userNews==null) {
	userNews = generateNews(uuid);
	Cache.set(uuid+"item.key", userNews );
}
```

<!--
> **Next:** [[Calling web services | JavaWS]]
-->
> **次ページ:** [[Web サービスの呼び出し | JavaWS]]