---
title: "@vercel/og で動的な画像を生成するガイド【Next.js】"
emoji: "🎱"
type: "tech"
topics:
  - "vercel"
  - "初心者向け"
  - "nextjs"
  - "ogp"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で、フルスタックアプリを構築しています。

@vercel/og は、Next.js 開発においては、広く浸透しているかと思いますが、
発表されて以来、継続的なアップデートが行われてきたようです！

そこで今回は、
改めて、**@vercel/og について調べた**ので、結果をまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：
・Next.js v14.2.5/ App Router

## @vercel/og とは？

OG Image Generation（@vercel/og）は、2022 年 10 月に vercel より発表されました。

https://vercel.com/blog/introducing-vercel-og-image-generation-fast-dynamic-social-card-images

上記のリリースブログから引用すると、

> ダイナミックなソーシャルカード画像を生成するための新しいライブラリである Vercel OG Image Generation を発表できることを嬉しく思います。
> このアプローチは、Vercel Edge Functions、WebAssembly、および HTML/CSS を SVG に変換するためのまったく新しいコアライブラリを使用することで、既存のソリューションよりも 5 倍高速です。

つまり、
**@vercel/og を使うことで、HTML/CSS のコードから、簡単に画像を生成することが可能です**！

主なユースケースとしては、
ページ毎に動的な OG 画像を生成する実装が、挙げられます。

具体的には、`ImageResponse`関数をインポートして、処理を書いていきます！

## @vercel/og の特徴

https://vercel.com/docs/functions/og-image-generation#technical-details

上記の vercel の公式ドキュメントより、
@vercel/og の特徴を、簡潔に紹介します！

### @vercel/og の実行環境

この記事の執筆時点（2024/08/23）で、
@vercel/og は、下記でサポートされています：

- Edge ランタイム
- Node.js ランタイム

開発元が、Vercel なので、Vercel や Next.js アプリとの相性が良いのは当然ですね。

**ただ、Cloudflare など、その他の環境でも動作します**！
（筆者は、GitHub Pages, Vercel, Cloudflare の環境で、動作することを確認しました）

導入としては、
**Next.js/App Router の場合**は、デフォルトで組み込まれています。
以下のコマンドより、インポート可能です:

```tsx
import { ImageResponse } from "next/og";
```

**Next.js/App Router 以外の場合**は、
以下のコマンドより、ライブラリを導入できます:

```zsh
npm i @vercel/og
```

### @vercel/og の技術的な詳細: Satori と resvg

https://vercel.com/docs/functions/og-image-generation#technical-details

@vercel/og の画像生成では、以下のような処理が行われています：

- [Satori](https://github.com/vercel/satori) によって、JSX（HTML, CSS） が SVG に変換される
- [resvg](https://github.com/yisibl/resvg-js) によって、SVG が PNG に変換される

なので、@vercel/og に依存したくない場合は、直接 Satori を使うことも可能です。
https://github.com/vercel/satori

ちなみに、Satori も Next.js と同じく、vercel が開発しているライブラリです！

**公式より、デモサイトが用意されています**。
JSX コードからの画像生成を試せるので、satori や@vercel/og の雰囲気をつかめますね。
https://og-playground.vercel.app/

## ImageResponse: Next.js で動的な画像を生成する手順

実際に、Next.js/ App Router で、
動的な画像を生成する手順を紹介します！

https://nextjs.org/docs/app/api-reference/functions/image-response

> ImageResponse コンストラクタを使用すると、JSX と CSS を使用して動的画像を生成できます。
> これは、オープングラフ画像、Twitter カードなどのソーシャルメディア画像を生成するのに便利です。

ドキュメントから引用した通り、
ImageResponse をインポートすることで、使用可能です！

また、実際に ImageResponse を用いて、動的な OG 画像を生成する場合は、
Next.js のファイルベースの設定を利用できます！

https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image#generate-images-using-code-js-ts-tsx

上記の、Next.js のファイル規約に従い、
ルートセグメントに `opengraph-image.'(js|ts|tsx)` を追加します。

公式より、サンプルコードを引用します

```tsx:app/opengraph-image.tsx
import { ImageResponse } from "next/og";

export const runtime = "edge";

// Image metadata
export const alt = "About Acme";
export const size = {
  width: 1200,
  height: 630,
};

export const contentType = "image/png";

// Image generation
export default async function Image() {
  // Font
  const interSemiBold = fetch(
    new URL("./Inter-SemiBold.ttf", import.meta.url)
  ).then((res) => res.arrayBuffer());

  return new ImageResponse(
    (
      // ImageResponse JSX element
      <div
        style={{
          fontSize: 128,
          background: "white",
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
        }}
      >
        About Acme
      </div>
    ),
    // ImageResponse options
    {
      // For convenience, we can re-use the exported opengraph-image
      // size config to also set the ImageResponse's width and height.
      ...size,
      fonts: [
        {
          name: "Inter",
          data: await interSemiBold,
          style: "normal",
          weight: 400,
        },
      ],
    }
  );
}
```

ImageResponse のコードの部分を簡略化すると、以下のようになります：

```tsx
import { ImageResponse } from 'next/og'

new ImageResponse(
  // 第１引数に、画像に変換したいJSXを受け取る
  element: ReactElement,

  // 第２引数に、サイズやフォントなどのオプションを受け取る
  options: {
    width?: number = 1200
    height?: number = 630
    emoji?: 'twemoji' | 'blobmoji' | 'noto' | 'openmoji' = 'twemoji',
    fonts?: {
   ~~
  }
)
```

こう見ると、シンプルですね 😎
上記で、`{title}`など、動的な値を使用することも、もちろん可能です。

https://zenn.dev/hiromu617/articles/c03fef6f4d6c6e

また、デザインに画像を使用することも可能です！
上記の記事では、svg 背景を使用して、「いい感じ」に作成する手順が解説されています。

## おまけ：ImageResponse の様々なオプション

https://nextjs.org/docs/app/api-reference/functions/image-response

上記の公式ページによると、
ImageResponse は、デフォルトで様々なオプションが用意されています。

```tsx
import { ImageResponse } from 'next/og'

new ImageResponse(
  element: ReactElement,
  options: {
    width?: number = 1200
    height?: number = 630
    emoji?: 'twemoji' | 'blobmoji' | 'noto' | 'openmoji' = 'twemoji',
    fonts?: {
      name: string,
      data: ArrayBuffer,
      weight: number,
      style: 'normal' | 'italic'
    }[]
    debug?: boolean = false

    // Options that will be passed to the HTTP response
    status?: number = 200
    statusText?: string
    headers?: Record<string, string>
  },
)
```

- 絵文字やテキストのフォントを変更したり
- TailwindCSS でスタイルを記述したり

柔軟なカスタマイズが可能です！

（**OG 画像を作成する場合の推奨サイズは、1200x630 ピクセル**です。）

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://vercel.com/blog/introducing-vercel-og-image-generation-fast-dynamic-social-card-images
https://vercel.com/docs/functions/og-image-generation#technical-details
https://nextjs.org/docs/app/api-reference/functions/image-response
https://nextjs.org/docs/app/api-reference/functions/image-response
https://zenn.dev/uzimaru0000/articles/satori-workers
https://zenn.dev/monicle/articles/f02e4a12da960b