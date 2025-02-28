---
title: "カスタムフックの紹介と導入【React】"
emoji: "⚛️"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "frontend"
  - "hooks"
  - "初心者向け"
published: true
publication_name: "b13o"
---

## はじめに

先日、React の勉強会で、カスタムフックの実装について取り上げました 🪝

コンポーネントからロジックを分離することで、
より保守性の高い React アプリケーションの構築に繋がります！

今回は、カスタムフック初心者の方向けに、
**「カスタムフックの利点と使用法」についてまとめました！**

カスタムフック導入の第一歩として、参考になれば嬉しいです 🙌

## React のカスタムフックとは？

https://ja.react.dev/learn/reusing-logic-with-custom-hooks

公式の紹介を要約すると、

> React には useState、useEffect など複数の組み込みフックが存在します。
> しかし、データの取得やユーザのオンライン状態の監視など、より特化した目的のためのフックが欲しいこともあります。
> アプリケーションの要求に合わせて独自のフックを作成することが可能です

**要は、コンポーネントから、ロジックを抽出するテクニックです！**
これにより、独自のフックとして使用できます。

同じく React 公式で挙げられている、
**カスタムフックの注意点**は、:
https://ja.react.dev/learn/reusing-logic-with-custom-hooks#when-to-use-custom-hooks

- 命名規則として、必ず"use"から始める（例：`useCounter`）
- React Hook を呼び出さない関数は、カスタムフックにする必要はない
- useEffect を使用する際は、カスタムフックでラップすることを検討する [ref](https://ja.react.dev/learn/reusing-logic-with-custom-hooks#when-to-use-custom-hooks:~:text=%E3%81%9F%E3%81%A0%E3%81%97%E3%80%81%E3%82%A8%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%A8%E3%81%8D%E3%81%AF%E5%B8%B8%E3%81%AB%E3%80%81%E6%9B%B4%E3%81%AB%E3%81%9D%E3%81%AE%E3%82%A8%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%A0%E3%83%95%E3%83%83%E3%82%AF%E3%81%AB%E3%83%A9%E3%83%83%E3%83%97%E3%81%99%E3%82%8B%E3%81%93%E3%81%A8%E3%81%A7%E3%82%88%E3%82%8A%E5%88%86%E3%81%8B%E3%82%8A%E3%82%84%E3%81%99%E3%81%8F%E3%81%AA%E3%82%89%E3%81%AA%E3%81%84%E3%81%8B%E3%80%81%E6%A4%9C%E8%A8%8E%E3%81%99%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%97%E3%81%A6%E3%81%8F%E3%81%A0%E3%81%95%E3%81%84)
- useMount のようなカスタムフックは作成すべきでない。より具体的なユースケースで使用する

## React におけるカスタムフックの利点

でも実際、
カスタムフックがどう役に立つのでしょうか、、？

### 1. コードの繰り返しを避ける

https://xn--97-273ae6a4irb6e2hsoiozc2g4b8082p.com/%E3%82%A8%E3%83%83%E3%82%BB%E3%82%A4/DRY%E5%8E%9F%E5%89%87/#google_vignette

> DRY(Don’t Repeat Your Self：繰り返しを避けること)原則は、プログラミングに関して守るべきとされている原則の中でも特に重要なものと言っていいでしょう。

複数のコンポーネントで同じようなロジックを使用する場合、それを毎回書くのは非効率です。
カスタムフックを使えば、一度定義したロジックを複数の場所で再利用できますよ。

**共通化することで、管理しやすく、人間にとっても理解やすいコードになりますね**！

### 2. 関心の分離

UI とロジックを分離することで、コードの見通しが良くなります。

**そもそも、React コンポーネントの主要な役割は、UI のレンダリングです**。

コンポーネントは UI の表示に集中し、ロジックはフックに委譲することで、
それぞれの責務が明確になりますね。

### 3. 独立したテスト

https://testing-library.com/docs/react-testing-library/api/#renderhook

ロジックが分離されていると、UI とは独立してテストができるようになります。
そして、カスタムフックは単なる関数なので、比較的簡単にテストを行えます。

**抽象化してロジックの中身を隠蔽しても、テストを書くと安心です**！

## React でカスタムフックを実装する例

さて、基本的な概念を理解したところで、
カスタムフックの、シンプルな実装例を紹介します：

### シンプルなカウンターフック

```tsx
import { useState } from "react";

const useCounter = (initialValue = 0) => {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount((prev) => prev + 1);
  const decrement = () => setCount((prev) => prev - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
};
```

### コンポーネントでの使用例

```tsx
import useCounter from "./hooks/useCounter";

const CounterComponent = () => {
  const { count, increment, decrement, reset } = useCounter();

  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={increment}>＋１</button>
      <button onClick={decrement}>−１</button>
      <button onClick={reset}>リセット</button>
    </div>
  );
};
```

## なぜカスタムフックなのか？

個人的には、カスタムフックの魅力は、**関心の分離**にあると考えています！

https://offers.jp/media/event-report/a_3060#outline-3

上記で紹介されている通り、
**1 コンポーネントと 1hooks という形で抜き出す方針**を、試してみるのもおすすめです。

再利用しなくても、単純に可読性を高めるために、
カスタムフックに抽出することも、有効だと思っています。

分厚いロジックや、テストしたいコアなビジネスロジックなどであれば、尚更ですね！

### コンポーネント分割の考え

ロジックを分離するというのは、
コンポーネントの分割とも似ていますね。

再利用しなくても、適切な粒度で切り分けることで、責務が明確になります。

コードの見通しも、良くなります。

**メンテナンス性を向上させるために、適切に関心を分離し、必要に応じてテストを書いていく、、**
**などの段階的なプロセスが、開発効率の向上につながるかなと！**

そして、抽象化してシンプルに扱えるようにする作業は、
エンジニアリングの醍醐味でもありますかね。

### ファサードパターン（Facade Pattern）の実現

https://www.techscore.com/tech/DesignPattern/Facade

> Facade パターンは、既存のクラスを複数組み合わせて使う手順を、「窓口」となるクラスを作ってシンプルに利用できるようにするパターンです。

ちなみに、カスタムフックは、複雑なロジックや処理を単純なインターフェースの背後に隠す、
ファサードパターンの一種と考えることもできます。

コンポーネント内で、関数や Hooks を直接呼び出していくのではなく、

- それらを束ねた「窓口」となる関数（カスタムフック）を用意し、それ１つを呼び出す
- 複雑なロジックや処理を、シンプルなインターフェースの背後に隠す
- この「窓口」に対して、重点的にドキュメントコメントやテスト書く

**この様にすると、管理しやすく、理解しやすいコードになりそうですね**！

## おわりに

**最後まで読んでいただき、ありがとうございます** 🥳

下記の、React ハンズオン勉強会での、振り返りのような記事ですが、
カスタムフック実践の第一歩として、当記事が参考になれば幸いです！

https://b13o.com/services/handson-workshop

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://ja.react.dev/learn/reusing-logic-with-custom-hooks
https://qiita.com/cheez921/items/af5878b0c6db376dbaf0
https://zenn.dev/luvmini511/articles/df410f137d1e21
