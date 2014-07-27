<!-- translated -->
<!--
# Working with sub-projects
-->
# サブプロジェクト

<!--
A complex project is not necessarily composed of a single Play application. You may want to split a large project into several smaller applications, or even extract some logic into a standard Java or Scala library that has nothing to do with a Play application.
-->
複雑なプロジェクトを、一つの Play アプリケーションにまとめる必要はありません。大きなプロジェクトは、小さなアプリケーションに分割できます。また、アプリケーションから独立したロジックは Java や Scala ライブラリに切り出すのもよいでしょう。

<!--
It will be helpful to read the [SBT documentation on multi-project builds](https://github.com/harrah/xsbt/wiki/Getting-Started-Multi-Project).  Sub-projects do not have their own build file, but share the parent project's build file.
-->
詳細については[「マルチプロジェクト・ビルドについての SBT ドキュメント」](http://scalajp.github.com/sbt-getting-started-guide-ja/multi-project/) [(原文)](https://github.com/harrah/xsbt/wiki/Getting-Started-Multi-Project) を参照してください。また、サブプロジェクトを定義するにあたって最も基本的なこととして、サブプロジェクト用のビルドファイルというものはありません。親のビルドファイルにサブプロジェクトを定義します。

<!--
## Adding a simple library sub-project
-->
## ライブラリをサブプロジェクトとして切り出す

<!--
You can make your application depend on a simple library project. Just add another sbt project definition in your `project/Build.scala` build file:
-->
アプリケーションからシンプルな「ライブラリプロジェクト」を切り出すことができます。`project/Build.scala` に、次のような sbt のプロジェクト定義を追加してください。

```
import sbt._
import Keys._
import PlayProject._

object ApplicationBuild extends Build {

  val appName         = "my-first-application"
  val appVersion      = "1.0"

  val appDependencies = Seq()
  
  val mySubProject = Project("my-library", file("modules/myLibrary"))

  val main = PlayProject(
    appName, appVersion, appDependencies
  ).dependsOn(mySubProject)
}
```

<!--
Here we have defined a sub-project in the application’s `modules/myLibrary` folder. This sub-project is a standard sbt project, using the default layout:
-->
これで、`modules/myLibrary` ディレクトリにサブプロジェクトを定義しました。サブプロジェクトは普通の sbt プロジェクトの一種であり、標準的なディレクトリ構成に従います。

```
modules
 └ myLibrary
    └ src
       └ main
          └ java
          └ scala 
```

<!--
When you have a sub-project enabled in your build, you can focus on this project and compile, test or run it individually. Just use the `projects` command in the Play console prompt to display all projects:
-->
ビルド設定でサブプロジェクトを有効にした場合、それぞれのプロジェクトを個別にコンパイル、テスト、実行することができます。Play コンソールで `projects` コマンドを実行すると、全てのプロジェクトが表示されます。

```
[my-first-application] $ projects
[info] In file:/Volumes/Data/gbo/myFirstApp/
[info] 	 * my-first-application
[info] 	   my-library
```

<!--
To change the current project use the `project` command:
-->
現在のプロジェクトを切り替えるには、`project` コマンドを使ってください。

```
[my-first-application] $ project my-library
[info] Set current project to my-library
>
```

<!--
When you run your Play application in dev mode, the dependent projects are automatically recompiled, and if something cannot compile you will see the result in your browser:
-->
Play アプリケーションを開発モードで起動している場合、依存するサブプロジェクトも自動的に再コンパイルされます。サブプロジェクトのコンパイルエラーも、ブラウザ上で確認できます。

[[subprojectError.png]]

<!--
## Splitting your web application into several parts
-->
## Webアプリケーションを複数のプロジェクトに分割する

<!--
As a Play application is just a standard sbt project with a default configuration, it can depend on another Play application. 
-->
Play アプリケーションはデフォルト設定に従った普通のsbtプロジェクトでもあるため、お互いに依存性を持たせることができます。

<!--
The configuration is very close to the previous one. Simply configure your sub-project as a `PlayProject`:
-->
設定方法は、サブプロジェクトの場合ととてもよく似ています。サブプロジェクトを `PlayProject` として定義すれば OK です。

```
import sbt._
import Keys._
import PlayProject._

object ApplicationBuild extends Build {

  val appName = "zenexity.com"
  val appVersion = "1.2"

  val common = PlayProject(
    appName + "-common", appVersion, path = file("modules/common")
  )
  
  val website = PlayProject(
    appName + "-website", appVersion, path = file("modules/website")
  ).dependsOn(common)
  
  val adminArea = PlayProject(
    appName + "-admin", appVersion, path = file("modules/admin")
  ).dependsOn(common)
  
  val main = PlayProject(
    appName, appVersion
  ).dependsOn(
    website, adminArea
  )
}
```

<!--
Here we define a complete project split in two main parts: the website and the admin area. Moreover these two parts depend themselves on a common module.
-->
これで、Web サイトと管理者向けサイトから構成される main プロジェクトを定義できました。また、両方のプロジェクトが common モジュールに依存しています。

<!--
If you would like the dependent projects to be recompiled and tested when you recompile and test the main project then you will need to add an "aggregate" clause.
-->
main プロジェクトを再コンパイルしてテストするとき、依存するプロジェクトが再コンパイルされてテストされるようにしたい場合は、"aggregate" 句を追加する必要があります。

```
  val main = PlayProject(
    appName, appVersion
  ).dependsOn(
    website, adminArea
  ).aggregate(
    website, adminArea
  )
}
```