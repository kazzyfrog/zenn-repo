---
title: "Next.js 14.2 で、サイトマップを設定するガイド"
emoji: "🗺️"
type: "tech"
topics:
  - "seo"
  - "初心者向け"
  - "nextjs"
  - "sitemap"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で静的サイトを、構築しています。

個人的に、サイトの SEO 周りの設定は、
毎回調べながら、そこまで深掘りせずに設定を完了しがちです。。

なので今回は、**Next.js の App Router で、サイトマップを設定する手順と、そこで調べたこと**をまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：
・Next.js v14.2.5/ App Router

## サイトマップとは？

https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview?hl=ja

上記ページの、Google 検索のドキュメントから引用すると、

> サイトマップとは、サイト上のページや動画などのファイルについての情報や、各ファイルの関係を伝えるファイルです。
> Google などの検索エンジンは、このファイルを読み込んで、より効率的にクロールを行います。
> サイトマップはサイト内の重要なページとファイルを検索エンジンに伝えるだけでなく、重要なファイルについての貴重な情報（ページの最終更新日やすべての代替言語ページなど）も提供します。

つまり、検索エンジンに対して、Web サイト内の情報を正確に伝えるためのファイルといえます！

### サイトマップは、必ず必要 ？

これに関しても、上記の[Google 検索ドキュメント](https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview?hl=ja#do-i-need-a-sitemap)に記載されていました！

> サイトの各ページが適切にリンクされていれば、Google は通常、サイトのほとんどのページを検出できます。

なので、必ずしも必要というわけではなさそうです！

より具体的な基準についても、記載されていました！
以下に、[同ページの](https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview?hl=ja#do-i-need-a-sitemap)簡単な要約を示します。

**サイトマップが必要になるのは、：**

- サイトのサイズが大きい場合
- サイトが新しく、外部からのリンクが少ない場合
- サイトに動画や画像などのリッチメディア コンテンツが多数含まれている、またはサイトが Google ニュースに表示されている場合

**サイトマップが必要ない可能性があるのは、：**

- サイトのサイズが小さい（約 500 ページ以下）場合
- サイト内の、すべてのページを内部リンクが網羅している場合
- 検索結果に表示させたいメディアファイル（動画、画像）や、ニュースページが多くない場合

## Next.js 14.2/ App Router で、sitemap.xml を設定する手順

サイトマップについて、ざっくりわかったところで、
実際に、Next.js 14.2 で、設定していきます。

https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap

上記の、Next.js の公式ドキュメントによると、
サイトマップの設定には、**大きく２通りの方法があります**：

### 1.app ディレクトリ内に、`sitemap.xml` ファイルを作成する

公式より、サンプルコードを引用します

```xml:app/sitemap.xml
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://acme.com</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>yearly</changefreq>
    <priority>1</priority>
  </url>
  <url>
    <loc>https://acme.com/about</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://acme.com/blog</loc>
    <lastmod>2023-04-06T15:02:24.021Z</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.5</priority>
  </url>
</urlset>
```

上記の通りで、設定することができます。

しかし、内容を手書きする必要があるので、
次に示す、サイトマップ生成を用いて設定するのが、**個人的にはおすすめです！**

### 2.app ディレクトリ内に、`sitemap.(js|ts)` ファイルを作成する

こちらも、公式より、サンプルコードを引用します

```ts:app/sitemap.ts
import { MetadataRoute } from 'next'

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: 'https://acme.com',
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 1,
    },
    {
      url: 'https://acme.com/about',
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: 'https://acme.com/blog',
      lastModified: new Date(),
      changeFrequency: 'weekly',
      priority: 0.5,
    },
  ]
}
```

このように、`sitemap.xml`を返す関数を作成することで、設定することが可能です。

### メモ:サイトマップに設定する属性について

https://www.sitemaps.org/ja/protocol.html
上記の、サイトマップのドキュメントによると、
設定すべき属性は以下の通りです：

- `<urlset>`（必須）: 現在のプロトコル
- `<url>`（必須）:各 URL を囲うタグ
- `<loc>`（必須）: ページの URL
- `<lastmod>`（必須）: 最終更新日
- `<changefreq>`（任意）: 更新頻度
- `<priority>`（任意）: ページの優先度

この属性に関しては、
以下の、Google 検索ドキュメントにも、詳しい記載がありました。
https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap?hl=ja#additional-notes-about-xml-sitemaps

> - 他の XML ファイルと同様に、すべてのタグ値をエスケープする必要があります。
> - Google は、<priority> と <changefreq> の値を無視します。
> - Google は、<lastmod> 値が一貫して正確であることを（ページの最終更新との比較などにより）検証できる場合に、この値を使用します。

なので、Google 基準で設定を行う場合は、
`<priority>` と `<changefreq>` の値は設定しなくても、問題なさそうです。

また、`<lastmod>`に関しても、正しく更新日を設定できる場合に、設定するのが良さそうですね

## Next.js で動的なページのサイトマップを生成する

動的なページの場合も、
同じように app ディレクトリ内に、`sitemap.(js|ts)` ファイルを作成します。

以下に、例を示します！

```ts:app/sitemap.ts
import { MetadataRoute } from 'next'

export default function sitemap(): Promise<MetadataRoute.Sitemap>  {
    const defaultPages: MetadataRoute.Sitemap = [
        {
        url: 'https://acme.com',
        },
        {
        url: 'https://acme.com/blog',
        },
        // other pages
  　　];

    const posts = await getAllPosts();

    const blogPages: MetadataRoute.Sitemap = posts.map((post) => ({
        url: `https://acme.com/blog/${post.slug}`,
        lastModified: new Date(post.date_modified),
  }));

  return [...defaultPages, ...blogPages];
}
```

上記では、動的ではない`defaultPages`と、動的に生成（`[slug]`）される`blogPages`に分けています！

このように設定することで、
動的なページも、一括で生成することができますね！

### メモ：Next.js /sitemap は、v13.3.0 で追加された

上記の、Next.js が提供する、sitemap に関する機能は、v13.3.0 にて追加されました！

それ以前は、外部ライブラリの[next-sitemap](https://github.com/iamvishnusankar/next-sitemap) などを使用して、
設定するケースが多かったかと思います！

詳しくは、下記のバージョン履歴を参照ください
https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap#version-history

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview?hl=ja
https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap
https://zenn.dev/mh4gf/scraps/21803bbf1c2d25
https://www.sitemaps.org/ja/protocol.html
https://dminhvu.com/post/nextjs-seo#sitemap
