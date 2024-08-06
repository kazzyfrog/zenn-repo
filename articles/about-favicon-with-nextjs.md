---
title: "Next.js 14.2 でファビコンを設定するガイド【6 種類】"
emoji: "😈"
type: "tech"
topics:
  - "web"
  - "初心者向け"
  - "nextjs"
  - "favicon"
  - "approuter"
published: true
---

## はじめに

現在、Next.js で静的サイトを、構築しています。

個人的に、web サイトのアイコン周りは、あまり深掘りせずに設定しがちです。
なので、いつも調べては、忘れてを繰り返しています。。

そこで今回は、
**Next.js の App Router で、ファビコンを設定する手順と、そこで調べたこと**をまとめました。

時間の節約になれば、嬉しいです :)

**検証環境**：
・Next.js v14.2.5/ App Router

## そもそも、web サイトにはどのようなアイコンが必要？

ファビコンの種類に関しては、以下の記事で詳しく解説されています。

https://techracho.bpsinc.jp/hachi8833/2024_02_09/108697

上記によると、必要なファイルは「**_5 個のアイコンと JSON ファイル 1 個_**」です！

内容を簡潔に要約すると、具体的には下記の 6 種類です：

- favicon.ico：safari 用
- icon.svg：モダンブラウザ用
- apple-touch-icon.png：Apple デバイス用
- icon-192.png：Android 用
- icon-512.png：Android 用
- manifest.json

manifest.json は、
PWA など、Web サイトをアプリとしてインストールするときに、ブラウザが用いる情報を記載したファイルです。

## Next.js 14.2 でファビコンを設定するガイド

必要なファイルがわかったところで、
Next.js の App Router で、ファビコンを設定していきます！

### 1.ファビコンの元となる画像を作成する

これに関しては、説明は不要ですね！

お好きなツールを使用して、ファビコンの元となる画像を、用意してください！
[canva](https://www.canva.com) などで、簡単に作成できます。

### 2.必要に応じて背景を透過する

（**背景を透過させたくない場合は、このステップを飛ばしてください。**）

:::message
（2024/8/7 追記）
Apple デバイス用の`apple-icon.png` については、
透過画像にせず、四角い背景付きにした方が、見た目崩れを防ぐことができます！
詳しくは、[こちら](https://zenn.dev/pacchiy/articles/e4dcd7bd29d387)のサイトを参照してください:)

コメント欄にて、教えて頂きました 🙏
:::

https://www.remove.bg/ja

画像作成時に使用したツールによっては、
背景を透過させての保存が、できない場合があります。

その場合は、上記のようなツールを使用して背景を透過できます！

### 3.ファビコンジェネレータを使用して、一括生成する

ファビコン生成形の無料ツールを使用することで、
複数種類のファビコンを一括生成できます。

https://favicon.io

上記のサイトでは、png 画像から、以下のファイルを一括で生成できます：

- android-chrome-192x192.png
- android-chrome-512x512.png
- apple-touch-icon.png
- favicon-16x16.png
- favicon-32x32.png
- favicon.ico
- site.webmanifest

### 4.svg ファイルを作成する

ファビコン生成形の無料ツールでは、
svg 形式の画像まで一括で生成できるものは、見つけられませんでした。

https://www.freeconvert.com/ja

なので、png 画像を svg 形式に変換する場合は、
上記のツールを使用できます！

#### メモ：svg viewr を使用して、svg アイコンの表示を確認する

必要に応じて、下記のサイトで、
正しく svg に変換されているかを確認できます！

https://www.svgviewer.dev

**svg 形式の画像は、ブラウザ上で描画されるため、様々なサイズでも画質が崩れにくいという特徴があります！**

svg 形式のロゴを用意しておくと、
サイト内のヘッダーなどでも利用しやすいので、便利ですね！

### 5.Next.js プロジェクトにファビコンを設定する

ここまでで、必要なファイル（**5 個のアイコンと JSON ファイル 1 個**）を用意できました！

あとは、Next.js の公式ドキュメントに従って、
ファイルベースの設定を行なっていきます！

https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons
https://nextjs.org/docs/app/api-reference/file-conventions/metadata/manifest

上記のドキュメントの通りですが、`app`ディレクトリの直下に、
以下のようにファイルを配置するだけで、設定を完了できます：

```md
src/
├─app/
| ├─apple-icon.png
| ├─favicon.ico
| ├─icon-192x192.png
| ├─icon-512x512.png
| ├─icon.svg
| ├─manifest.json
| └//
//
```

#### メモ：Next.js のファイルベースの設定について

Next.js の App Router の、ファイルベースの設定では、
以下のようなファイル名を検出し、サイトの`<head`>要素に適切なタグを自動的に追加されます！

- apple-icon.png
- favicon.ico
- icon.svg
- manifest.json

**なので、拡張子の前のファイル名は、上記に合わせて変更する必要があります！**

ただし、下記のファイルに関しては、
Next.js のファイルベースの設定ではなく、manifest.json にて指定するので、別のファイル名でも問題ないです！

- icon-192x192.png
- icon-512x512.png

この場合、manifest.json には、次のように記載します：

```json:app/manifest.json
{
  "name": "Next.js App",
  "short_name": "Next.js App",
  "description": "Next.js App",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#fff",
  "theme_color": "#fff",
  "icons": [
    { "src": "/icon-192x192.png", "type": "image/png", "sizes": "192x192" },
    { "src": "/icon-512x512.png", "type": "image/png", "sizes": "512x512" }
  ]
}
```

上記では、`icons` 以外は、[Next.js 公式](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/manifest)のサンプルを記載しています。
適宜、独自の web サイトの情報に書き換えてください！

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://techracho.bpsinc.jp/hachi8833/2024_02_09/108697
https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons
https://nextjs.org/docs/app/api-reference/file-conventions/metadata/manifest
