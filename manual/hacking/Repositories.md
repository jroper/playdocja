<!-- translated -->
<!--
# Artifact repositories
-->
# アーティファクト・レポジトリ

<!--
## Typesafe repository
-->
## Typesafe レポジトリ

<!--
All Play artifacts are published to the Typesafe repository at <http://repo.typesafe.com/typesafe/releases/>.
-->
全ての Play 関連のアーティファクトは <http://repo.typesafe.com/typesafe/releases/> にある Typesafe レポジトリにパブリッシュされています。

<!--
> **Note:** it's a Maven2 compatible repository.
-->
> **Note:** このレポジトリは Maven2 形式です。

<!--
To enable it in your sbt build, you must add a proper resolver (typically in `plugins.sbt`):
-->
sbt ビルドでこのレポジトリを有効化したいときは、正しいリゾルバを追加（通常は `plugins.sbt` 内に）する必要があります。


```
// The Typesafe repository
resolvers += "Typesafe Releases" at "http://repo.typesafe.com/typesafe/releases/"
```

<!--
## Accessing snapshots
-->
## Snapshot を利用する

<!--
Snapshots are published daily from our [[Continuous Integration Server|ThirdPartyTools]] to the Typesafe snapshots repository at <http://repo.typesafe.com/typesafe/snapshots/>.
-->
Snapshot は Play の [[継続的インテグレーションサーバ|ThirdPartyTools]] から <http://repo.typesafe.com/typesafe/snapshots/> にある Typesafe Snapshots レポジトリへ、一日一回パブリッシュされています。

```
// The Typesafe snapshots repository
resolvers += "Typesafe Snapshots" at "http://repo.typesafe.com/typesafe/snapshots/"
```

