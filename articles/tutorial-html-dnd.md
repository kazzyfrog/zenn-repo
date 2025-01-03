---
title: "HTML Drag and Drop APIの紹介と導入【React】"
emoji: "⚛️"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "frontend"
  - "dragandrop"
  - "初心者向け"
published: true
---

## はじめに

Web アプリケーション開発において、ユーザー体験を向上させる重要な機能の 1 つに、ドラッグ&ドロップが挙げられます。

シンプルなマウス操作で要素を移動したり、ファイルをアップロードしたり、
直感的な操作性を実現することができます。

今回は、**HTML Drag and Drop API について調査した**ので、基礎的な内容をまとめました！
時間の節約になれば、嬉しいです 🙌

## HTML Drag and Drop API とは？

https://developer.mozilla.org/ja/docs/Web/API/HTML_Drag_and_Drop_API

HTML Drag and Drop API は、ブラウザネイティブのドラッグ&ドロップ機能を実現するための API です。

要素をドラッグ可能にし、ドロップゾーンを設定することで、インタラクティブな操作を実装できます。

当記事執筆時点（2024/01/03）で、
主要なブラウザで広くサポートされており、追加のライブラリなしで実装可能です。

## HTML Drag and Drop API の使い方

そもそも、ドラッグ&ドロップとは、、？

### ドラッグとは？

要素（アイテム）をクリックして、マウスボタンを押したまま移動させる動作のことです。
ユーザーが要素を掴んで、別の場所に移動させたいと意図している状態と言えます。

### ドロップとは？

ドラッグしている要素を、目的の位置に移動させる（落とす）動作のことです。
なので、実装時には、ドロップ可能なエリアを設定します！

### 基本的な使い方のまとめ

基本的な使い方は以下のとおりです！

ドラッグ可能な要素に、

- `draggable="true"`属性を設定
- `onDragStart`: ドラッグ開始時
  - `e.dataTransfer.setData()`でデータを設定
- `onDragEnd`: ドラッグ終了時
  - クリーンアップ処理など

ドロップ可能な要素に、

- `onDragOver`: ドラッグ中
  - `e.preventDefault()`が必須
  - ドラッグ中は継続的に発火
- `onDragLeave`: ドラッグ要素が領域外に出たとき
- `onDrop`: ドロップ時
  - `e.preventDefault()`が必須
  - `e.dataTransfer.getData()`でデータを取得

移動に伴うデータの受け渡しは、

- `dataTransfer`オブジェクトを使用してドラッグ要素からドロップゾーンへデータを渡す
  - `setData()`: データの設定
  - `getData()`: データの取得

## React への導入手順

さて、基本的な使い方を理解したところで、
React への導入を確認してみましょう！

下記は、簡略化したサンプルコードです：

### 1. ドラッグ可能なコンポーネントの作成

```jsx
const DraggableItem = ({ id, content }) => {
  const handleDragStart = (e) => {
    e.dataTransfer.setData("text/plain", id);
  };

  return (
    <div
      draggable="true"
      onDragStart={handleDragStart}
      className="draggable-item"
    >
      {content}
    </div>
  );
};
```

### 2. ドロップゾーンの作成

```jsx
const DropZone = ({ onDrop }) => {
  const handleDragOver = (e) => {
    e.preventDefault();
  };

  const handleDrop = (e) => {
    e.preventDefault();
    const id = e.dataTransfer.getData("text/plain");
    onDrop(id);
  };

  return (
    <div onDragOver={handleDragOver} onDrop={handleDrop} className="drop-zone">
      ここにドロップ
    </div>
  );
};
```

### 3. 親コンポーネントでの状態管理

```jsx
const DragDropContainer = () => {
  const [items, setItems] = useState([
    { id: "1", content: "アイテム1" },
    { id: "2", content: "アイテム2" },
  ]);

  const handleDrop = (id) => {
    // ドロップ時の状態更新処理
    setItems(/* 新しい配列 */);
  };

  return (
    <div>
      {items.map((item) => (
        <DraggableItem key={item.id} id={item.id} content={item.content} />
      ))}
      <DropZone onDrop={handleDrop} />
    </div>
  );
};
```

## なぜ HTML Drag and Drop API なのか？

個人的には、HTML Drag and Drop API の魅力は、
**ネイティブの機能によるシンプルな実装**にあると考えています！

ここまでで紹介したとおり、：

- 追加のライブラリが不要で、バンドルサイズを抑えられる
- ブラウザネイティブの機能なので、パッケージの依存関係もない

なので、使い分けとしては、

- シンプルなドラッグ&ドロップ機能であれば、HTML Drag and Drop API で対応
- より高度なカスタマイズには、**@dnd-kit**などを検討
- （なお、`react-beautiful-dnd` は、本記事執筆時点ですでに非推奨）

## おわりに

**最後まで読んでいただだき、ありがとうございます** 🥳

開発中に、調べたことの記録のような記事ですが、
少しでも参考になれば、嬉しいです！

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://developer.mozilla.org/ja/docs/Web/API/HTML_Drag_and_Drop_API
https://zenn.dev/kazuwombat/articles/f23b47f168f1d0
https://zenn.dev/castingone_dev/articles/dndkit20231031
