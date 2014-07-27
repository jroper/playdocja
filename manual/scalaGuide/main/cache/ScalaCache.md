<!-- translated -->
<!--
# The Play cache API
-->
# Play キャッシュ API

<!--
The default implementation of the Cache API uses [[EHCache| http://ehcache.org/]]. You can also provide your own implementation via a plug-in.
-->
キャッシュ API のデフォルト実装は [[EHCache| http://ehcache.org/]] です。その他の実装はプラグイン経由で利用することができます。

<!--
## Accessing the Cache API
-->
## キャッシュ API へのアクセス

<!--
The cache API is provided by the `play.api.cache.Cache` object. It requires a registered cache plug-in.
-->
キャッシュ API は `play.api.cache.Cache` オブジェクトとして提供されています。このオブジェクトを利用するためには、キャッシュプラグインが既に登録されている必要があります。

<!--
> **Note:** The API is intentionally minimal to allow several implementation to be plugged. If you need a more specific API, use the one provided by your Cache plugin.

Using this simple API you can either store data in cache:
-->
> **Note:** 様々な実装をプラグインできるように、キャッシュ API の機能は意図的に最小限に絞りこまれています。より特殊な API が必要な場合、独自のキャッシュプラグインにその API を持たせるとよいでしょう。

このシンプルな API を利用して、データをキャッシュに保存するには次のようなコードを記述します。


```
Cache.set("item.key", connectedUser)
```

<!--
And then retrieve it later:
-->
そして、保存したデータを後で取得するためには、次のようなコードを記述します。

```
val maybeUser: Option[User] = Cache.getAs[User]("item.key")
```

<!--
There is also a convenient helper to retrieve from cache or set the value in cache if it was missing:
-->
値がキャッシュに保存されていればそれを取得し、そうでなければ保存するという機能を持つ便利なヘルパ関数もあります。

```
val user: User = Cache.getOrElse[User]("item.key") {
  User.findById(connectedUser)
}
```

<!--
To remove an item from the cache use the `remove` method:
-->
`remove` メソッドでデータをキャッシュから削除することができます。

```
Cache.remove("item.key")
```

<!--
## Caching HTTP responses
-->
## HTTP レスポンスのキャッシュ

<!--
You can easily create smart cached actions using standard Action composition. 

> **Note:** Play HTTP `Result` instances are safe to cache and reuse later.

Play provides a default built-in helper for standard cases:
-->
標準的なアクション合成の方法を使って、簡単にスマートなキャッシュ機能を備えたアクションを実装できます。

> **Note:** Play の HTTP `Result` インスタンスは安全にキャッシュ、再利用することができます。

また、標準的なユースケースであれば、Play 組み込みのヘルパが利用できます。

```
def index = Cached("homePage") {
  Action {
    Ok("Hello world")
  }
}
```

<!--
Or even:  
-->
こんなこともできます。

```
def userProfile = Authenticated { user =>
  Cached(req => "profile." + user) {      
    Action { 
      Ok(views.html.profile(User.find(user)))
    }   
  }
}
```

<!--
> **Next:** [[Calling web services | ScalaWS]]
-->
> **次ページ:** [[Web サービスの呼び出し | ScalaWS]]
