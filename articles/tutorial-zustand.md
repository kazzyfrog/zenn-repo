---
title: "状態管理ライブラリ Zustand の紹介と導入【React】"
emoji: "🐻"
type: "tech"
topics:
  - "zustand"
  - "react"
  - "nextjs"
  - "状態管理"
  - "初心者向け"
published: true
publication_name: "b13o"
---

React/ Next.js 開発において、重要なテーマの１つに、状態管理が挙げられます。

`useState`フックを使用したベーシックなケースから、
状態管理ライブラリを導入するまで、さまざまなプラクティスが存在します。

先日、React の勉強会で、
**状態管理ライブラリ Zustand について取り上げた**ので、基礎的な内容をまとめました！

時間の節約になれば、嬉しいです 🙌

## Zustand とは？

https://zustand-demo.pmnd.rs

Zustand は、React アプリケーションのための、軽量な状態管理ライブラリです。
シンプルなフックを作成し、簡単に使い始めることができます。

当記事執筆時点（2024/10/25）で、
**グローバルな状態管理ライブラリの中では、Redux に次いで Zustand は人気です。**
https://npmtrends.com/jotai-vs-mobx-vs-recoil-vs-redux-vs-zustand

## グローバルな状態管理の必要性は？

そもそも、なぜ、Zustand のようなライブラリで、
グローバルな状態管理を行う必要があるのか疑問に思っている場合は、このセクションを確認してください 👍

### なぜ単純な useState だけでは不十分なのか？

React では、`useState`で状態を管理し、props で子コンポーネントへと値を渡していく形が、最もベーシックなデータの流れだと思います。

下記の画像のように、シンプルなコンポーネントツリーであれば、それで問題ないと言えます。

![](https://storage.googleapis.com/zenn-user-upload/55a4fdb916c2-20241024.webp)
_出典： [React 公式](https://ja.react.dev/learn/understanding-your-ui-as-a-tree)_

しかし、プロジェクトの規模が増すにつれ、
コンポーネントツリー全体は、複雑になっていきます。

**そうなったら、複数のコンポーネントを経由して、props を親から子へ、孫へ、、と、バケツリレー的に渡していく必要があります。**

将来的に、別のコンポーネントから、管理している状態を参照・更新したくなったら、
経由する全てのコンポーネントに、変更を加えることにもなります。

それでは、メンテナンス性が良くないですね。

### useContext による解決と限界

上記の、コンポーネントツリーが複雑化した際の問題の解決策として、
React 公式で提供されている、`useContext`フックがあります。
https://ja.react.dev/reference/react/useContext

これにより、コンテクストプロバイダでラップしたツリー内であれば、どれだけコンポーネントの階層が深くても、`useContext`フックを使い、値を参照・更新することができます。

```jsx
// useContextを使用した例
import { createContext, useState, useContext } from "react";

const AmountContext = createContext(null);

const ParentComponent = () => {
  const [amount, setAmount] = useState(0);
  return (
    <AmountContext.Provider value={{ amount, setAmount }}>
      <ChildComponent1 />
      <ChildComponent2>
        <GrandChild1 />
        <GrandChild2>
          <GreatGrandChild1 /> {/* amountを参照可能 */}
          <GreatGrandChild2 />
        </GrandChild2>
      </ChildComponent2>
      <ChildComponent3 />
    </AmountContext.Provider>
  );
};
```

`props`のバケツリレーを、解消できましたね！

しかし、`useContext`にも、問題があります。
**それは、Context の値が変更されると、コンテキストプロバイダー内の全ての子コンポーネントが再レンダリングされ、パフォーマンスが悪化することです。**

この問題は、[memo](https://ja.react.dev/reference/react/memo) 化と、複数のプロバイダーを使用することで、解決することができますが、
それにより、以下のような[Provider タワー](https://zenn.dev/uhyo/articles/provider-tower-to-recoil)が発生し、依存関係が複雑化するという、別の問題が発生してきます。

```jsx
const App = () => (
  <ThemeProvider>
    <UserProvider>
      <SettingsProvider>
        <NotificationProvider>
          {/* アプリケーションのコンテンツ */}
        </NotificationProvider>
      </SettingsProvider>
    </UserProvider>
  </ThemeProvider>
);
```

https://www.frontendundefined.com/posts/monthly/react-context-global-state/
上記でも、この解決策は、「イケてない」という旨の内容が書いてあります。
（日本語訳）

> TLDR; React コンテキストと状態フック (useState または useReducer) を使用してグローバル状態を管理すると、デフォルトではパフォーマンスが低下します。手動でコードを最適化してパフォーマンスを向上させることもできますが、非常に面倒です

なので、グローバルな状態管理には、`useContext`での実装を頑張るより、
高いパフォーマンスで状態を管理できるライブラリを導入する、というのがより一般的な解決策と言えます！

## Zustand の導入手順

さて、状態管理ライブラリの必要性について理解したところで、
実際に Zustand を使用してみます！

https://zustand.docs.pmnd.rs/getting-started/introduction
公式ドキュメントを参考に、手順を示します！

### 1. Zustand をインストール

```bash
npm install zustand
```

ちなみに、React インストール時の初期画面を参照すると、

![](https://storage.googleapis.com/zenn-user-upload/bb76305b5524-20241025.png)
_出典： Vite + React インストール時の初期画面_

上記では、useState を使用して、`App.jsx` 内に`count`という状態を定義し、
ボタンクリックに応じて、`setCount`という関数で値を更新しています。

**この時、`App.jsx`コンポーネント内に、状態と更新用の処理が、定義されていることを確認してください。**

### 2. `store`を作成

Zustand は、コンポーネントツリーと切り離した場所（`store`）にて、
状態（`state`）と、更新用の処理（`action`）を管理します。

```tsx
// store/useStore.jsx

import { create } from "zustand";

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

export default useStore;
```

上記では、コンポーネントツリーの外側で、状態と更新用の処理が定義されています。
これにより、`useStore`フックを使用して、どのコンポーネントからでも、状態にアクセスすることができます。

### 3. コンポーネントから `useStore`フックを使う

必要なコンポーネント内で、`useStore`フックを追加します 👍

```jsx
// コンポーネントからuseStoreフックを使う

const App = () => {
  const { count, increment } = useStore();
  return (
    <div>
       <p>{count}</p>
       <button onClick={increment}>+1</buttoon>
    </div>
  );
};
```

より実践的なプラクティスは、
以下のように、`store`内の特定の値や操作を、指定して読み込むことです。

```jsx

const IncrementButton = () => {
  const increment = useStore((state) => state.increment);
  return (
    <div>
       <button onClick={increment}>+1</buttoon>
    </div>
  );
};
```

これにより、`IncrementButton`コンポーネントは、`increment`のみを指定して読み込んでいるため、`count`の値が更新されても、再レンダリングされません。

このように、不要な再レンダリングを防ぐために、
手軽に微調整することができます！

## なぜ Zustand なのか？

**個人的には、Zustand が人気の理由は、とにかく使いやすさにあると考えています！**

コンポーネントツリーの外側に`store`を作成し、
あとはフックを使用して、好きな場所から呼び出すだけです！

概念的にも、すでに、React Hooks に慣れていれば、
今までの知識を踏まえて、直感的に使い始めることができます：

- useState と同様に、イミュータブル（不変性）に基づいて、状態を管理する
- useState や Redux と同じように、`Immer` を使用して、ネストしたオブジェクトの更新処理を簡略化できる
- Redux/ useReducer と同様に、状態（`state`）と更新用の処理（`action`）を、近い場所（コロケーション）で管理する

学習コストも、Redux より低いです。
何より、Zustand は Redux によく似ており、Flux/Redux パターンをベースに、さらに簡略化したライブラリです！

## おわりに

正直、読み方が何度も覚えられないので、Zustand を避けていた期間がありました。。
しかし触ってみたら、手軽さがとてもお気に入りです！

筆者と同じように、Redux や Recoil での状態管理で止まっている開発者は、
ぜひ１度触ってみることをお勧めします。

**最後まで読んでいただだき、ありがとうございます** 🥳

下記の、React ハンズオン勉強会での、振り返りのような記事ですが、
少しでも参考になれば、嬉しいです！

https://b13o.com/services/handson-workshop

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://github.com/pmndrs/zustand
https://zustand-demo.pmnd.rs
https://zenn.dev/uhyo/articles/provider-tower-to-recoil
https://www.frontendundefined.com/posts/monthly/react-context-global-state/
