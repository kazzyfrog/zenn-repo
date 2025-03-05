---
title: "Auth.js v5 の紹介と導入【Next.js】"
emoji: "⚛️"
type: "tech"
topics:
  - "nextjs"
  - "typescript"
  - "nextauth"
  - "authjs"
  - "初心者向け"
published: true
publication_name: "b13o"
---

## はじめに

先日、Next.js の勉強会で、Auth.js による認証の実装を取り上げました 🔑

ウェブアプリに、セキュアな認証周りの実装を一から構築するのは、大変でもあります。
しかし、セキュリティに関わるので、重要です！

今回は、Auth.js 初心者の方向けに、
**「Auth.js（旧 NextAuth.js）の利点と使用法」についてまとめました！**

技術選定の参考になれば、嬉しいです 🙌

## Auth.js（旧 NextAuth.js） とは？

https://authjs.dev/

Auth.js は、ウェブアプリケーションに簡単に認証機能を追加できる、オープンソースの認証ライブラリです。

もともとは、Next.js 向けに開発されていたので、「NextAuth.js」という名前で知られていました。

当記事執筆時点の、最新バージョン V5（β 版）からは、
Auth.js という名前で、Next.js に限定しない形で提供されています！

https://authjs.dev/getting-started/introduction

公式のイントロより、
Auth.js の主な特徴を挙げると、以下の通りです：

:::message

- Next.js での使いやすさ
- 複数の認証プロバイダー（Google、GitHub、Apple など）のサポート
- マジックリンク（パスワードレス）認証のサポート
- JWT やデータベースセッション管理
- 柔軟なコールバックとイベントフック
- セキュリティ対策が標準装備（CSRF 保護など）

:::

公式の紹介を見る限り、なかなか良さそうですね！
では、実際に、Next.js への導入を確認していきます 👀

## Auth.js v5 を Next.js プロジェクトへ導入する

https://authjs.dev/getting-started/installation

上記の公式ガイドを参考に、
Next.js のプロジェクトに、Auth.js を導入してみます！

### 1. Next.js プロジェクトの準備

まず、Next.js プロジェクトを作成します：

```bash
npx create-next-app@latest my-auth-app
cd my-auth-app
```

### 2. Auth.js のインストール

```bash
npm install next-auth@beta
```

### 3. セットアップ環境の記載

トークンとメール検証ハッシュを暗号化するために、ライブラリが使用するランダムな値を生成します：

```bash
npx auth secret
```

### 4. 設定ファイルの作成

Auth.js の設定ファイルとオブジェクトを作成します。

```tsx:./auth.ts
import NextAuth from "next-auth";

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [],
});
```

ルートハンドラーも追加します：

```tsx: ./app/api/auth/[...nextauth]/route.ts
import { handlers } from "@/auth"; // Referring to the auth.ts we just created
export const { GET, POST } = handlers;
```

### 5. ミドルウェアの追加

セッションを維持するためにミドルウェアを追加します！

```tsx: ./middleware.ts
export { auth as middleware } from "@/auth";
```

**あとは、お好きな認証方式の設定を追加していくだけです**！

GitHub や Google アカウントを使った認証は、一般的ですね。

今回は、GitHub アカウントでの認証を紹介します：

## GitHub アカウントでの認証

https://authjs.dev/getting-started/providers/oauth-tutorial

### 1. **GitHub のダッシュボードに OAuth アプリを登録する**

まず、GitHub 側で、使用するアプリケーションを登録する必要があります！

https://authjs.dev/guides/configuring-github?framework=next-js

GitHub の設定に関しては、
**上記の公式ガイドで、スクリーンショット付きの手順が紹介されています**！

### 2. 環境変数の追加

GitHub に登録すると、クライアント ID とクライアントシークレットが取得できるので、
環境変数ファイルに追加します：

```:.env.local
AUTH_GITHUB_ID={CLIENT_ID}
AUTH_GITHUB_SECRET={CLIENT_SECRET}
```

### 3. 設定ファイルに、GitHub プロバイダーを追加

```tsx: ./auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [GitHub],
});
```

### 4. 最後に、ログインボタンを作成しましょう！！

```tsx
import { signIn } from "@/auth";

export default function SignIn() {
  return (
    <form
      action={async () => {
        "use server";
        await signIn("github");
      }}
    >
      <button type="submit">Signin with GitHub</button>
    </form>
  );
}
```

**これで、GitHub によるログインの実装は完了です**🎉

## Auth.js の利点とは？

筆者の考える利点は、
**開発者体験と安全性を両立させているところです！**

- **シンプルな API**: 複雑な認証ロジックを、数行のコードで実装可能
- **セキュリティ**: CSRF 保護、JWT ハンドリング、セッション管理などセキュリティのベストプラクティスが標準装備
- **拡張性**: カスタムプロバイダー、カスタムデータベースアダプターなど高度なカスタマイズが可能

上記の特徴により、
**（セキュリティ面で重要度の高い）認証周りの機能を、簡単に実装できます**。

特に、リソースが限られる個人開発者・スタートアップには、嬉しいポイントです

例えば、
**サービス初期は、ユーザーのデータを自分のサービスで抱えずに、認証を導入できます**。

GitHub や Google, Supabase など、さまざまな連携がサポートされている点もありがたいです。

そして、高いカスタマイズ性があるので、
段階的に拡張していくことが可能ですね！

## おわりに

**最後まで読んでいただき、ありがとうございます** 🥳

下記の、React/Next.js ハンズオン勉強会での、振り返りのような記事ですが、
Auth.js への入門として、当記事が参考になれば幸いです！

https://b13o.com/services/handson-workshop

そして、もし、間違いや補足情報などがありましたら、
ぜひコメントを追加してください！

Happy Hacking :)

## 参考

https://authjs.dev/
https://zenn.dev/arsaga/articles/3f5bce7c904ebe
https://zenn.dev/ph3nac/articles/next-auth-credential
https://qiita.com/jokota/items/fb8fb7fbb45737513922
