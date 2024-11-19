---
title: "Next.js 15 で rel=canonical を使用して URL を正規化するガイド"
emoji: "🖥"
type: "tech"
topics:
  - "seo"
  - "初心者向け"
  - "nextjs"
  - "canonical"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で、ブログ付きの Web サイトを構築しています。

個人的に、サイトの SEO 周りの設定は、
毎回調べながら、そこまで深掘りせずに設定を完了しがちです。。

なので今回は、
**Next.js で rel="canonical" を使用して URL を正規化する手順と、そこで調べたこと**をまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：
・Next.js v15.0.3/ App Router

## rel="canonical" による正規化とは？

https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls?hl=ja#rel-canonical-link-method

上記ページの、Google 検索のドキュメントから引用すると、

> rel="canonical" link 要素（canonical 要素とも呼ばれます）は、HTML の head セクションで使用される要素で、別のページがそのページの正規コンテンツであることを示しています。

**つまり、重複するコンテンツ（ページ）が存在する場合に、検索エンジンに対して「正規の URL」を指定するための、HTML 要素です 👍**

## URL 正規化のユースケースと利点

これに関しても、上記の[Google 検索ドキュメント](https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls?hl=ja#reasons-to-specify-a-canonical-url)に記載されています。

上記より、正規 URL を指定すべき理由を紹介すると、：

- **検索結果でユーザーに表示したい URL を指定するため**
- **類似ページや重複ページについてシグナルを統合するため**
- **特定のコンテンツのトラッキング指標をシンプルにするため**
- **重複するページのクロールに要する時間を削減するため**

例えば、次のようなユースケースで有効ですね！

Zenn などの、素晴らしいプラットフォームで記事を書くのと同時に、
ポートフォリオとして、自分のサイトに同一の記事を掲載したい場合。
複数媒体にクロスポストしながら、**重複コンテンツであることを、明示できます！**

他にも、
アプリ内で、ページネーションや検索機能を実装した際、
意図せず大量のページが、検索にインデックスされる事を防ぐこともできます！！

## Next.js 15 で URL を正規化する手順

`rel="canonical"` による正規化について、ざっくりわかったところで、
実際に、Next.js 15 で、設定していきます。

https://nextjs.org/docs/app/api-reference/functions/generate-metadata#alternates

上記の、Next.js の公式ドキュメントによると、
サイトマップの設定には、**大きく２通りの方法があります**：

### 1.静的`metadata`オブジェクトで設定する方法

公式より、サンプルコードを引用します：

```js: layout.js | page.js
export const metadata = {
  alternates: {
    canonical: 'https://nextjs.org',
  },
}
```

上記の通り、`layout` または `page` ファイル内にて、`metadata`オブジェクトを定義することで、
`rel="canonical"` を指定することが可能です。

これにより、以下のように出力されます！

```html:<head> output
<link rel="canonical" href="https://nextjs.org" />
```

### 2.動的`generateMetadata`関数で設定する方法

動的ルートの場合は、`generateMetadata`関数を使用して、
動的に canonical URL を生成できます：

```js
// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }): Promise<Metadata> {
  return {
    alternates: {
      canonical: `/blog/${params.slug}`,
    },
  };
}
```

**どちらの方法でも、[`alternates`](https://developer.mozilla.org/ja/docs/Web/HTML/Attributes/rel#alternate)のプロパティの中に、canonical URL を記載する必要があることに、注意してください！**

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls?hl=ja
https://qiita.com/tatsuyayamakawa/items/3ce2a3eee3951f25b6b6
https://nextjs.org/docs/app/api-reference/functions/generate-metadata#alternates
https://developer.mozilla.org/ja/docs/Web/HTML/Attributes/rel#alternate
