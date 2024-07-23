---
title: "JSON-LD is 何?"
emoji: "📘"
type: "tech"
topics:
  - "seo"
  - "初心者向け"
  - "json"
  - "jsonld"
  - "linkeddata"
published: true
---

## はじめに

現在、Next.js 製の静的サイトを、構築しています。

その際、「JSON-LD」の設定が、[Next.js 公式ドキュメント](https://nextjs.org/docs/app/building-your-application/optimizing/metadata#json-ld)にて、紹介されていました。
どうやら、SEO に有効な設定、、、らしいです！

今回は、**JSON-LD とは何か？ どんなメリットがあるのか？　などをリサーチした**ので、
結果をまとめました。

時間の節約になれば、嬉しいです。

## 要約

忙しい人向け:)

- JSON-LD は、構造化データを表現するための、JSON を拡張したフォーマット。
- データを構造化し、意味を付与することで、異なるデータソース間の統一性を保ち、検索エンジンの理解を助ける。
- これにより、さまざまな Web サイトにおける、データの互換性と再利用性が向上する。

### 筆者の理解

個人的に、たくさんの記事を読まないと、いまいちピンときませんでした。
なので、先に、筆者の理解をまとめます：)

要は、 API から取得した JSON データを、フロントエンド側で画面に表示する場合、
そのデータは、プロジェクトの慣習や個人の好みで、独自の変数名やデータのフォーマットが使われている。

それを、**JSON-LD を使用し、構造化することで、**
**文法的なフォーマット・意味を Web 横断的に統一する**ということ。

そうすることによって、異なるサイト同士のデータを関連付け、データの再利用性が向上する。
また、検索エンジンがウェブページの内容を理解しやすくなる（SEO の向上）。

つまり、人にとっても、コンピューターにとっても、
扱いやすいデータを記述することができる、フォーマット。。！

---

（本編です）

## JSON-LD とは？

少しピンとこなかったので、いろんな説明を引用してみます。

[wiki](https://ja.wikipedia.org/wiki/JSON-LD) によると、

> JSON-LD (JavaScript Object Notation for Linked Data) は JSON を利用して Linked Data を表現する手法である。

GPT 4o によると、

> JSON-LD（JavaScript Object Notation for Linked Data）は、JSON（JavaScript Object Notation）を拡張したもので、データを構造化し、異なるデータソース間でリンクするためのフォーマットです。JSON-LD を使うことで、ウェブ上のデータを意味的に関連付けることができます。

以下の記事によると、
https://zenn.dev/esnir/articles/basic-jdon-ld

> JSON-LD は、Linked data を JSON 形式で記述する規格、または JSON 形式で書かれた Linked data を指します。
> Linked data とは、あるデータをコンピュータにとって読みやすく、かつ他の Web 上のデータと容易に結び付けられるようにしようという考え方です。

以下の記事によると、
https://dminhvu.com/post/nextjs-jsonld

> JSON-LD は、Linked Data の JavaScript Object Notation の略です。これは、人間が読み書きしやすく、機械が解析して生成しやすい軽量データ交換形式です。

## JSON-LD の役割と利点

上述した記事によると、ポイントは以下のように整理できます：

- **データの標準化**: 異なるサイトやプロジェクトごとに異なる変数名やフォーマットを標準化し、データの互換性を向上させます。
- **構造化データと意味論の追加**: データの文法的なフォーマットと意味論を定義し、データが何を意味するのかが明確になり、コンピュータがデータを理解するのが容易になります。
- **異なるサイト同士のデータの関連付け**: 異なるデータソースからのデータをリンクし、統合することができます。
- **検索エンジンの理解を助ける**: 検索エンジンがページの内容をより深く理解し、リッチスニペット（例えば、商品レビューやイベント情報など）が表示され、UX が向上します。

### メモ：検索エンジンの理解（SEO）

こちらは、下記の Google のドキュメントでも紹介されていました：
https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data?hl=ja

> Google は、Google 検索がページのコンテンツを正確に理解するよう努めています。構造化データを使用してページの意図を伝えると、Google はそのページをより正確に理解できるようになります。
> （中略）
> 構造化データを追加することで、よりユーザーの興味をひく検索結果を表示できるようになり、ウェブサイトの利用も増えることが期待されます。これはリッチリザルトと呼ばれます。

## JSON-LD の例

より具体的に、知りたい方に向けて、
実際の JSON-LD の例を AI で生成したので、紹介していきます。

例えば、異なる API から以下のような JSON データを取得したとします。

```json
// API1のレスポンス
{
  "name": "Jane Doe",
  "job": "Professor",
  "phone": "(425) 123-4567",
  "website": "http://www.janedoe.com"
}

// API2のレスポンス
{
  "fullName": "Jane Doe",
  "position": "Professor",
  "contactNumber": "(425) 123-4567",
  "site": "http://www.janedoe.com"
}
```

これらのデータを JSON-LD を使って標準化し、統一的に扱うことができます。

```json
{
  "@context": "http://schema.org",
  "@type": "Person",
  "name": "Jane Doe",
  "jobTitle": "Professor",
  "telephone": "(425) 123-4567",
  "url": "http://www.janedoe.com"
}
```

このようにすることで、異なる API から取得したデータが統一されます。
また、データの意味が明確になり、検索エンジンや他のアプリケーションがデータを理解しやすくなります。

### JSON-LD の基本構造

JSON-LD の基本的な構造は、以下の通りです
（この構造により、データの文法的なフォーマットと意味論を定義します。）

- **コンテキスト (`@context`)**: データのキーが何を意味するのかを定義します。
- **識別子 (`@id`)**: リソースを一意に識別するための ID を指定します。
- **タイプ (`@type`)**: リソースの種類を示します。

```json
{
  "@context": "http://schema.org",
  "@type": "Person",
  "@id": "http://example.com/person/1234",
  "name": "Jane Doe",
  "jobTitle": "Professor",
  "telephone": "(425) 123-4567",
  "url": "http://www.janedoe.com"
}
```

その他、
`name`, `jobTitle`, `telephone`, `url` は、Schema.org で定義されたプロパティを使用して、人物の名前、職業、電話番号、ウェブサイトを示しています。

## おまけ：Next.js での JSON-LD の設定

最後に、Next.js/ appRouter で JSON-LD を設定するには、
layout.js または page.js にて、`<script>`タグとしてレンダリングすることで可能です！

公式ドキュメントから、サンプルコードを引用します。

```ts:app/products/[id]/page.tsx
export default async function Page({ params }) {
  const product = await getProduct(params.id);

  const jsonLd = {
    "@context": "https://schema.org",
    "@type": "Product",
    name: product.name,
    image: product.image,
    description: product.description,
  };

  return (
    <section>
      {/* Add JSON-LD to your page */}
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      {/* ... */}
    </section>
  );
}
```

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://nextjs.org/docs/app/building-your-application/optimizing/metadata#json-ld
https://ja.wikipedia.org/wiki/JSON-LD
https://zenn.dev/esnir/articles/basic-jdon-ld
https://dminhvu.com/post/nextjs-jsonld
https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data?hl=ja
