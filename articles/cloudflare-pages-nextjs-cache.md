---
title: "Next.js の API レスポンスをキャッシュする【Cloudflare Pages】"
emoji: "📦"
type: "tech"
topics:
  - "nextjs"
  - "cloudflare"
  - "cache"
  - "frontend"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で、フルスタックアプリを構築しています。

Cloudflare Pages にデプロイした際、
**API レスポンスのキャッシュの設定で、少し手間取りました。**

そこで今回は、
Cloudflare Pages での Next.js のキャッシュ設定について調査した内容をまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：

Next.js v14.2.5/ App Router
（Next.js v15.1.0 でも同様の挙動であることを確認）

## 前提）Cloudflare Pages への Next.js デプロイで遭遇したエラー

2024/12/26 時点で、
**動的なセグメントをビルド時に生成（SG）したい場合、ビルドエラーになる**ようです。

以下の、公式の通りですが、Route Handlers は、GET メソッドのキャッシュ（SG）が可能です。

https://nextjs.org/docs/app/building-your-application/routing/route-handlers#behavior

> Route Handlers are not cached by default. You can, however, opt into caching for GET methods. Other supported HTTP methods are not cached. To cache a GET method, use a route config option such as export const dynamic = 'force-static' in your Route Handler file.

（訳）

> ルートハンドラーはデフォルトでキャッシュされていません。ただし、GET メソッドのキャッシュを選択できます。他のサポートされている HTTP メソッドはキャッシュされていません。GET メソッドをキャッシュするには、ルート ハンドラー ファイルで export const dynamic = 'force-static' などのルート設定オプションを使用します。

筆者は今回、

- `/api/v1/ssg/[slug]/route.ts` というファイルで、
- `export const dynamic = 'force-static'` を設定しています
- 合わせて、`generateStaticParams` を実行している状態です。

このような実装で、開発環境・vercel 上では問題なく動作します。

しかし、Cloudflare では、以下のようなエラーに遭遇しました：

```bash
⚡️ ERROR: Failed to produce a Cloudflare Pages build from the project.
⚡️
⚡️      The following routes were not configured to run with the Edge Runtime:
⚡️        - /api/v1/ssg/[slug]
⚡️
⚡️      Please make sure that all your non-static routes export the following edge runtime route segment config:
⚡️        export const runtime = 'edge';
⚡️
⚡️      You can read more about the Edge Runtime on the Next.js documentation:
⚡️        https://nextjs.org/docs/app/building-your-application/rendering/edge-and-nodejs-runtimes
```

動的なページには、`export const runtime = 'edge';` をつけてください！と言われてしまいました。。！

https://developers.cloudflare.com/pages/framework-guides/nextjs/ssr/troubleshooting/#generatestaticparams

公式に従い、以下の記述を追加しても、結果は変わらずです。。！

```tsx
export const dynamicParams = false;
```

なので、SG で静的な API エンドポイントを生やす実装は一旦諦めました。

## Cloudflare におけるキャッシュ管理とは？

他のデータキャッシュの方法を、探してみます 👀

https://developers.cloudflare.com/pages/framework-guides/nextjs/ssr/caching/

Cloudflare Pages では、
[Workers KV](https://developers.cloudflare.com/kv/)  または [Cache API](https://developers.cloudflare.com/workers/runtime-apis/cache/)  に、キャッシュを管理・読み込みをするように、 Next.js アプリを設定できます。

**デフォルトは、Cache API が有効になっているので、特に追加の設定は不要で使えます！**

## **Cache API を使用したキャッシュ**

https://developers.cloudflare.com/workers/runtime-apis/cache/

上記の公式ページで、
Cache API では、Cloudflare ネットワークのキャッシュの読み書きを、細かく制御できると、書かれています。

実装も意外と簡単そうです！
ポイントは、：

- `caches.open` メソッドで、キャッシュを作成・管理できます
- `cache.put` メソッドで、実際にデータをキャッシュに保存できます

実際のコード例は、以下のとおりです 🧐

```tsx
// キャッシュキーの生成（URLをそのまま使用）
const cache = await caches.open("lgtm-images");
const cacheKey = new Request(request.url);

~~

// キャッシュの確認
let response = await cache.match(cacheKey);
if (response) {
  return response;
}

~~

// キャッシュの保存
await cache.put(cacheKey, response.clone());
```

## ヘッダーオプションでキャッシュを設定

https://developers.cloudflare.com/cache/concepts/cdn-cache-control/

上記によると、
ヘッダーオプションで、CDN レベルでのキャッシュを設定できるようです。

なので：

- `CDN-Cache-Control` ヘッダーで、キャッシュの設定がを１年間に設定します。
- なお、デフォルトで `cache-control: public,max-age=31536000,immutable` （1 年間）は設定されています

最終的に、以下の設定をヘッダーに追加しました：

```tsx
headers: {
  "CDN-Cache-Control": "public, max-age=31536000, immutable",
},
```

これで、デフォルトのキャッシュに加え、CDN レベルでのキャッシュ期間も指定できました！
↓

![キャッシュの動作確認](https://github.com/user-attachments/assets/176b3ea3-eba8-4da1-a078-8706721a906f)

１度生成されたデータが、キャッシュされているので、
キャッシュが「**HIT**」していることが確認できますね！！！

[今回コミットしたソースコード](https://github.com/lgtm-factory/lgtm-factory/commit/c0f10742af89e3107abcc00eda0d9dc9b344f825)

これで、 Next.js の SG を使わなくても、
Route Handlers の、GET メソッドのキャッシュ（簡易的 SG）が可能になりました！

意外と簡単でしたね 🫠

## おわりに

[![LGTM Factory](https://lgtm-factory.pages.dev/api/v1/lgtm-images?theme=beer-glass&text=Thanks+%3B%29&emoji=%F0%9F%93%A6&color=%23fcd34d)](https://lgtm-factory.pages.dev)

**最後まで読んでいただだき、ありがとうございます** 🥳

下記の開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

https://lgtm-factory.pages.dev/

そして、**オリジナルのフリー LGTM 画像を生成できる、オープンソースのサイト**を、
リリースしました 🎉📦

新年のコードレビューに、ご活用ください！

[![LGTM Factory](https://lgtm-factory.pages.dev/api/v1/lgtm-images?theme=simple-emoji-browser&text=LGTM+Factory&emoji=%F0%9F%8E%8D%F0%9F%90%8D%F0%9F%A7%A7&color=%23fcd34d)](https://lgtm-factory.pages.dev)

Happy Hacking :)
