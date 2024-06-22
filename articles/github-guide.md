---
title: "GitHub初心者が中級者になる「それっぽい使い方」"
emoji: "🚀"
type: "tech"
topics:
  - "git"
  - "github"
  - "初心者向け"
  - "oss"
  - "オープンソース"
published: true
---

先日、コミュニティ内で共同開発を行い、GitHub 初心者向けのチュートリアルを作成しました。

初心者のうちは Git や GitHub を学習したら、
その後は、成果物を掲載する程度の使用にとどまりがちです。

なので、この記事では、
**GitHub 初心者が中級者になるための、それっぽい使い方を、テンポよく紹介していきます。**

普段からモチベーション高く GitHub を活用できたら良さそうですね！

対象:

- Git/ GitHub の基本的な使い方は知っている初心者
- Git/ GitHub の様々なプラクティスを知りたい方

## はじめに

Git/ GitHub の初期設定・基本的な使い方に関しては、この記事の範囲外となります。
必要に応じて、下記を参照してください：
https://qiita.com/mt-blue-sou/items/e28ae0b27a7911c302d5
https://qiita.com/uhooi/items/c26c7c1beb5b36e7418e

## 1.`.github`リポジトリを作成する

まず初めに、**`.github`リポジトリ**の前に、**`.github`ディレクトリ**について、触れておきます。

### `.github`ディレクトリとは？

**`.github`ディレクトリでは、GitHub の機能を拡張するための、さまざまな設定ファイルを管理することができます。**

具体的には、GitHub 上のリポジトリ内に、`.github`という名前のディレクトリを作成することで、以下のような設定を行うことができます:

- Issue テンプレートの設定
- Pull Request テンプレートの設定
- GitHub Actions の設定
- 行動規範（`CODE_OF_CONDUCT.md`）の設置
- 貢献ガイドライン(`CONTRIBUTING.md`)の設置

上記以外にも、様々な設定が可能です。詳しくは、下記を参照してください。
https://zenn.dev/morinokami/articles/dot-github-directory

### `.github`リポジトリとは？

さて、`.github`ディレクトリにて、
GitHub の活用に関する様々な設定ができることがわかりました。

その上で、**`.github`リポジトリでは、デフォルトの設定を１箇所で管理しておくことができます。**
これによって、自分が所有する全てのリポジトリに対して、`.github` リポジトリの設定が適用されるようになります。

詳しくは、以下のドキュメントを参照してください。
https://docs.github.com/ja/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file

なので、`.github`リポジトリを作成し、以下のような設定をしておくと、
**全てのリポジトリで利用できるので便利**です：

- Issue テンプレートの設定
- Pull Request テンプレートの設定

### Issue テンプレートの設定

その名の通りですが、リポジトリで新しい Issue を作成するときのテンプレートを設定できます。

Issue テンプレートの作成方法は、以下の通りです：

- リポジトリのルートディレクトリに、`issue_template.md`を作成する
- docs ディレクトリ内に`issue_template.md`を作成する →`docs/issue_template.md`
- .github ディレクトリ内に`issue_template.md`を作成する →`.github/issue_template.md`
- ルート、docs、.github ディレクトリ内に`ISSUE_TEMPLATE`サブディレクトリを作成する →`.github/ISSUE_TEMPLATE/issue_template.md`

詳しくは、以下のドキュメントを参照してください。
https://docs.github.com/ja/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository

個人的には、最後の`.github/ISSUE_TEMPLATE/issue_template.md`の形式を採用するのがおすすめです。
なぜなら、`ISSUE_TEMPLATE`サブディレクトリを作成することで、複数種類のテンプレートが設定可能になるからです。

以下のように、必要に応じて使い分けながら、追加していくことができます。

- 機能追加のテンプレート
- バグ報告のテンプレート
- 英語版のテンプレート

Issue テンプレートの内容に関しては、以下の記事が参考になります。
https://zenn.dev/sutamac/articles/5a262f0096176a#issueの作成について

### Pull Request テンプレートの設定

Issue と同様に、Pull Request を作成する際のテンプレートも設定できます。

作成方法に関しても、Issue テンプレートとほとんど同じで、`pull_request_template.md`を作成するだけです。

詳しくは、以下のドキュメントを参照してください。
https://docs.github.com/ja/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository

こちらも、複数のテンプレートを使い分けることが可能です。
**ただし、Pull Request の場合は、複数の中から１つテンプレートを指定する際に、クエリパラメーターを URL に追加する必要があります。**

具体的には、Pull Request 作成画面の URL 末尾に、以下のように URL を手動で追加することで、テンプレートを利用できます：
（ファイル名は、任意に命名したファイル名です）

```
?&template=pull_request_template_01.md
```

**Issue テンプレートと違って、毎回上記の URL を追記する必要があるので、Pull Request のテンプレートでは、１種類のみ用意する方が楽かと思います。**

また、Pull Request テンプレートの内容に関しては、以下の記事が参考になります。
https://qiita.com/suzuki-hoge/items/3a568dff36fd981082ba#git-template-for-pull-request-pullrequest作成時の説明欄にテンプレートを設定する

## 2.GitHub Flow で手順書を作成する

初心者のうちは、よく使う git コマンド付きの手順書があると、安心です。

なので、**もし、`.github`リポジトリを作成したら、ついでに GitHub に関する自分用のドキュメントを作成するのが、個人的におすすめです。**

手順書を作成するに当たって、２つの git の運用方法・ブランチ戦略を紹介します。

### git-flow とは？

git の運用方法・ブランチ戦略として、おそらく最も有名なものが git-flow です。

これは主に、5 つのブランチを使用します：

- master（本番環境用）
  　- 現在では、main という名前が推奨されています。
- develop（開発環境用）
- feature（機能別の作業用）
- release（リリース準備用）
- hotfix（緊急バグ修正用）

各ブランチの役割が明確ですね。
長期的で大規模なプロジェクトに適しているといえます。

詳しくは、以下の記事にて、考案者の記事が日本語訳されています。
https://qiita.com/homhom44/items/9f13c646fa2619ae63d0

ただし、「ブランチを切る」ことに慣れていないうちは、git-flow は少々複雑に感じます。

**なので、個人で普段活用する場合は、次の GitHub flow で良いと思います。**

### GitHub flow とは？

GitHub flow では、主に 2 つのブランチを使用します：

- main（本番環境用）
- feature（作業用）

ブランチが２つだけなので、シンプルですね。

より詳しくは、以下の記事を参照してください。
https://techblog.insightedge.jp/entry/branch-strategy-github-flow

そして、実際に、GitHub flow で手順書を作成すると、以下のようになります：

---

1. main ブランチから、作業用ブランチを作成 & 移動する。
   ```
   git switch -c feature/add-xxx
   ```
2. 実際に作業を行い、変更をコミットする。
   ```
   git add .
   git commit -m "xxxの実装"
   ```
3. 変更をリポートリポジトリに、プッシュする
   ```
   git push origin HEAD
   ```
4. プルリクエストを作成する
5. 作業用ブランチから、main ブランチに戻る
   ```
   git switch main
   ```
6. main ブランチを最新の状態に更新する
   ```
   git pull origin main
   ```
7. 作業し終わったブランチを削除する
   ```
   git branch -d feature/add-xxx
   ```

---

このようなドキュメントを用意しておけば、１連の開発の流れが明確ですね、
**main に直接コミットするのではなく、ブランチを切るクセをつけることは、重要です。**

上記のような、１連の流れを体験できる GitHub 初心者向けのチュートリアルを作成したので、
良かったら活用してみてください。
https://github.com/first-contributions-ja/first-contributions-ja.github.io

## 3.TIL リポジトリを作成する

TIL とは、Today I Learned の略であり、訳したら「今日学んだこと」です。

（具体的な起源は、見つけられませんでしたが、）
世界中の開発者の間で、おそらく 2012~3 年から行われている、GitHub を使った学習記録のプラクティスの１つです。

詳しくは、以下の記事を参照してください。
https://qiita.com/nemui_/items/239335b4ed0c3c797add

TIL には、一般的に以下のようなメリットがあります；

- 細かいアウトプットを習慣化できる
- GitHub と、マークダウンの記述に慣れることができる
- GitHub に草を生やして、学習の過程・取り組みを客観的に見える化できる

開発者の間では、「毎日草を生やそう！」的な風習やら、「積極的に技術記事を書こう」的な風習があったりするので、細かいアウトプットに慣れることは重要ですね。

より具体的な TIL の体験談に関しては、以下の記事が参考になります。
https://blogs.lisb.direct/entry/2016-03-28-100000

## 4.プロフィール README を作成する

GitHub 上で、自分のアカウント名と同じリポジトリを作成すると、README.md ファイルの内容が、プロフィール画面に表示されます。

詳しくは、以下のドキュメントを参照してください。
https://docs.github.com/ja/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme

また、このプロフィールを充実させるためのツールも、たくさんあります。
**オシャレなグラフや、ラベルなどをプロフィールに並べることで、一気に GitHub を使い込んでいる感を出せるのでおすすめです！**

詳しくは、以下の記事で紹介されています：
https://qiita.com/Keichan_15/items/7d0595369d6b6e321ede

## 5.オープンソースプロジェクトに参加する

オープンソースとは、誰でも自由に使って、学び、修正し、
ライセンスで定められた範囲内で自由に再配布できるということを意味します。

オープンソースの概要について、詳しくは、以下を参照してください。
https://opensource.guide/ja/starting-a-project/#オープンソースとはなにでありなぜそれを行うのか

そして、ほとんどの場合、オープンソースのプロジェクトは、誰でも自由に開発に参加することができます。

**オープンソースのプロジェクトに対して貢献（コントリビュート）することは、
他の開発者との共同開発を通して、自身のスキルやキャリアを高める素晴らしい方法です。**

初心者でも簡単に参加できるオープンソースプロジェクトを以前リサーチしたので、
興味があれば、ぜひ参加してみてください！
https://zenn.dev/kazzyfrog/articles/3cef24a46374af

## おわりに

最後まで読んでいただき、ありがとうございます 😎
気になることが１つでもあれば、ぜひ実践してみてください！

また、他にも「GitHub 中級者っぽいなー」と感じるような、
それっぽい使い方・取り組み・ポイントなどがあれば、ぜひ教えてください！！

**最後に告知です** 📣
初心者でも簡単にコントリビュートできる、オープンソースのサイトを共同開発しました！！
https://first-contributions-ja.github.io/
実践的な GitHub の使い方を学びながら、OSS 参加を体験できます！！

**参加者が増えるたびに、Web サイト上が変化するようになっているので、
良かったら参加してみてください！**

Happy Hacking !
