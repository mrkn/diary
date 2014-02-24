---
title: Jenkins のプラギンを作ってみたくなった
tags: java, jenkins
---

Android アプリ開発をやり初めてから Java にまた慣れてきたこともあって、今度は Jenkins の Plugin を作ってみたくなった。

Jenkins のプラグインを開発するためには何が必要か調べてみた。だいぶ前に調べたときは maven が必須だったはずだけど、[いまは Gradle を使ってビルドできる](https://jenkins-ci.org/node/363)そうだ。
具体的な方法は[この Wiki のページ](https://wiki.jenkins-ci.org/display/JENKINS/Gradle+JPI+Plugin)に書いてある。その内容を適当に訳したものを以下に書いておく。

## 必要条件

- Gradle 1.0 milestone 6 以上
- プラグインが Jenkins 1.420 以上を対象にビルドされること

## 初期化

プラグイン用のプロジェクトディレクトリは、プラグイン名が `foo` だったら、`foo` または `foo-plugin` にする必要がある。そして、`build.gradle` に次の内容を書く:

~~~~ groovy
apply from:"https://raw.github.com/jenkinsci/gradle-jpi-plugin/master/install"
// Or copy the contents of that URL into your build.gradle directly.

// Whatever other plugins you need to load.

groupId = "org.jenkins-ci.plugins"
version = "0.0.1-SNAPSHOT"    // Or whatever your version is.
description = "A description of your plugin"

jenkinsPlugin {
    coreVersion = '1.420'                                               // Version of Jenkins core this plugin depends on.
    displayName = 'Hello World plugin built with Gradle'                // Human-readable name of plugin.
    url = 'http://wiki.jenkins-ci.org/display/JENKINS/SomePluginPage'   // URL for plugin on Jenkins wiki or elsewhere.
    gitHubUrl = 'https://github.com/jenkinsci/some-plugin'              // Plugin URL on GitHub. Optional.

   // The developers section is optional, and corresponds to the POM developers section.
   developers {
       developer {
           id 'abayer'
           name 'Andrew Bayer'
           email 'andrew.bayer@gmail.com'
       }
   }                           

}
~~~~

もし追加で必要なリポジトリが存在するなら、それを `jenkinsPlugin` セクションの後ろに追記する。

## ビルド

`gradle celan jpi` を実行する。

## テスト

- `gradle test` で全ユニットテストを実行できる。maven と違ってテストは自動的に実行されない。
- 手動テスト用に Jenkins を起動したい場合は `gradle server` を実行する。Jenkins はポート 8080 で起動する。

## リリース

- 自動的にリリースすることはないので、今の状態がリリース可能かどうかは各自で管理すること。
- `gradle clean uploadArchives` を実行すればリリースできる。このコマンドは、プラグインをビルドし、Jenkins の Maven リポジトリへデプロイする。
  - Jenkins Maven リポジトリへアクセスするためのユーザ情報を、`~/.jenkins-ci.org` に以下のように書いておくこと。

    ~~~~
    userName=yourusername
    password=yourpassword
    ~~~~

  - `gradle clean deploy` でもリリースできる。`deploy` タスクは `uploadArchives` のエイリアスになってる。

## 例

- [Associated File Plugin](https://github.com/jenkinsci/associated-files-plugin) は Gradle でビルドとリリースが行なわれている。
- [Git plugin](https://github.com/jenkinsci/git-plugin) はまだ Maven で運用されてるけど、`build.gradle` が追加されている。
- [Hello World plugin](https://github.com/jenkinsci/hello-world-plugin) も参考になるはず。
