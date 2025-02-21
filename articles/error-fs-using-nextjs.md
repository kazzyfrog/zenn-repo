---
title: "Next.js で fs モジュールが使えない時に確認すること"
emoji: "💚"
type: "tech"
topics:
  - "nodejs"
  - "初心者向け"
  - "nextjs"
  - "vercel"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で、フルスタックアプリを構築しています。

開発中に、**fs モジュールがうまく読み込めないエラーが発生しました**。

そこで今回は、
エラーの解消と、その際に調べたことをまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：
・Next.js v14.2.5/ App Router

## エラー：Module not found: Can't resolve 'fs' - NextJS

開発中に、`npm run dev`コマンドで、ローカルサーバーを起動した際に、
以下の箇所でエラーが発生しました。

```ts:api/route.ts
import fs from "fs";
```

まさに同様のスレッドが、Next.js の GitHub 上にもありました:
https://github.com/vercel/next.js/discussions/54176

どうやら、API routes に限らず、page ファイルでも同様のエラーが出るようです！

## 結論:`fs`は、Node.js 環境でしか動かない

**`fs`は、Node.js に組み込まれたモジュール**なので、
Node.js 環境以外では動かない、、というだけです！

言い換えれば、:

- エッジランタイム環境では、動かない
- クライアントサイドの環境でも、動かない

### 今回発生したエラーの原因:`fs`は、エッジランタイム環境では動かない

https://nextjs.org/docs/app/api-reference/edge

公式のページ内にちゃんと、書いてありました。。🫠🫠

> [サポートされていない API](https://nextjs.org/docs/app/building-your-application/rendering/edge-and-nodejs-runtimes#unsupported-apis)
> エッジランタイムには、次のようないくつかの制限があります。
>
> - ネイティブの Node.js API はサポートされていません。たとえば、ファイルシステムを読み書きすることはできません。

今回の、筆者のケースでは、
API の処理を、エッジランタイム環境にて行うようにしています。

つまり、**`fs`は、Node.js とは異なるエッジランタイム上では動かない**ので、
エラーが出ていたようです！

（原因がわかった後では、すごい当たり前ですね。。🫠）

なので、同様に、
**クライアントサイドも Node.js 環境ではないので、`fs`をインポートしようとするとエラーが発生します**。

## 解説:クライアントバンドルと`fs`

上記のエラー解決にあたって、調べたことは以下の通りです。
さらに深掘りしたい箇所があれば、適宜参照してください！

### Next.js でクライアントバンドルを構築する

https://gotohayato.com/content/553/

上記によると、
クライアントサイドで、`fs`を使用していなくても、インポートされているだけで、同様のエラーが発生するようです。

その際は、
**`getStaticProps()`や`getServerSideProps()`内で、`fs`を使用することで、回避できます**。

もしくは、下記によると、
**クライアントにバンドルする、モジュールの設定を追加することでも、エラーを回避できるようです**。

https://techblg.app/articles/how-to-handle-module-not-found-cant-resolve-fs-nextjs/
https://stackoverflow.com/questions/64926174/module-not-found-cant-resolve-fs-in-next-js-application

その際は、`next.config.js`ファイルに、以下の記述を追加します：

```js
module.exports = {
  webpack5: true,
  webpack: (config) => {
    config.resolve.fallback = { fs: false };

    return config;
  },
};
```

### node:fs とは ？

https://nodejs.org/api/fs.html

ちなみに、
fs は、ファイルシステム（File system）の略です。

下記のサイトによると、
https://jsprimer.net/use-case/nodecli/read-file/

> node:fs モジュールは、Node.js でファイルの読み書きを行うための基本的な関数を提供するモジュールです。

つまり、ローカルのファイルやディレクトリを読み込む際に役立ちます。

`fs`は、Node.js 環境で使用することに、注意が必要ですね！

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

下記の開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

https://b13o.com

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://gotohayato.com/content/553/
https://techblg.app/articles/how-to-handle-module-not-found-cant-resolve-fs-nextjs/
https://stackoverflow.com/questions/64926174/module-not-found-cant-resolve-fs-in-next-js-application
https://nextjs.org/docs/app/api-reference/edge
https://nodejs.org/api/fs.html
https://jsprimer.net/use-case/nodecli/read-file/
