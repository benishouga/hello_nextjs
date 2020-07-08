# Next.js のチュートリアルをやる

## お題１ とりあえず Example からプロジェクトを作って少し編集するっぽい

https://nextjs.org/learn/basics/create-nextjs-app

node は 10.13 以上が必要。

### プロジェクト作る

```
$ npm init next-app nextjs-blog --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
```

### アプリを起動する

```
$ npm run dev

> learn-starter@0.1.0 dev /home/xxxxx/work/hello_nextjs/nextjs-blog
> next dev

[ wait ]  starting the development server ...
[ info ]  waiting on http://localhost:3000 ...
Browserslist: caniuse-lite is outdated. Please run next command `yarn upgrade`
[ ready ] compiled successfully - ready on http://localhost:3000
[ wait ]  compiling ...
Attention: Next.js now collects completely anonymous telemetry regarding usage.
This information is used to shape Next.js' roadmap and prioritize features.
You can learn more, including how to opt-out if you'd not like to participate in this anonymous program, by visiting the following URL:
https://nextjs.org/telemetry

[ ready ] compiled successfully - ready on http://localhost:3000
```

Next.js のチュートリアル、途中クイズが出題されて応えるような仕組みになっていて面白い。。。

### ページを編集する

Example としては `pages/index.js` が表示されているのでこれを修正していく。

アプリを起動したままページを編集したらホットリロードが走る様子。

## お題２ ページ遷移を追加する

https://nextjs.org/learn/basics/navigate-between-pages

ページは以下の感じを想定

- `/` へ アクセスしたら `pages/index.js` を表示 (すでにこちらは動く)
- `/posts/first-post` へ アクセスしたら `pages/posts/first-post.js` を表示

### ページを追加

`first-post.js` を `pages/posts` の中に作成

内容は以下の通り。

```jsx
export default function FirstPost() {
  return <h1>First Post</h1>;
}
```

JSX 使って書く。React と同じ。ただ、 `import * as React from "react"` 的なのは不要の様子。

サーバー起動し `http://localhost:3000/posts/first-post` を表示すると作成したページが表示される。

ファイルの配置されているパスが、そのまま URL パスにも対応する様子。

### index.js に追加したページへのリンクを配置する

画面遷移のリンクを配置する場合は以下のコンポーネントを利用する

```
import Link from 'next/link'
```

以下のように利用する

```jsx
Read <Link href="/posts/first-post"><a>this page!</a></Link>
```

Link タグの中に a タグを配置する形みたい。少し違和感あるが気にしない。

### Link タグについて

Link タグで画面遷移を作った場合 クライアントサイド でその遷移が行われる。
（Chrome Dev Tools で HTML の背景を変更してら、ページ遷移しても維持される）

<Link href="…"> の代わりに <a href="…"> で記述すると通常のリンクと同様にサーバーサイド描画で画面遷移となる。

Next.js ではコードを分割して配信する仕組みが自動で入り、大量のページを有するアプリケーションでも軽快に動作するとのこと。

またページ表示後、そのページからリンクされるページについてはプリフェッチが行われるため、実際にリンクをクリックした際の遷移は高速に動作する。

className などを使ってリンクを装飾したい場合は Link タグに属性を追加するのではなく a タグに属性を追加する必要があるとのこと。 (https://github.com/vercel/next-learn-starter/blob/master/snippets/link-classname-example.js)

## お題３ CSS を使って画面を装飾しよう的なもの

https://nextjs.org/learn/basics/assets-metadata-css

### ページタイトルを追加

以下の記述を `pages/posts/first-post.js` に追加

```js
import Head from "next/head";
```

```jsx
<Head>
  <title>First Post</title>
</Head>
```

### CSS Style の追加

以下のような記述で スタイル を追加できる

```jsx
<style jsx>{`
  …
`}</style>
```

例: タイトル(h1)の背景を黄色に

```jsx
<style jsx>{`
  h1 {
    background-color: yellow;
  }
`}</style>
```

書きっぷりには css の他 scss や Tailwind CSS なども標準でサポートしている様子。

### レイアウトを共通化するための layout.js を追加

```jsx
function Layout({ children }) {
  return <div>{children}</div>;
}
export default Layout;
```

このレイアウトを使うように `first-post.js` を修正します。

```jsx
import Head from "next/head";
import Link from "next/link";
import Layout from "../../components/layout";

export default function FirstPost() {
  return (
    <Layout>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="..">
          <a>Back to home</a>
        </Link>
      </h2>
    </Layout>
  );
}
```

`Head` タグはどこにあっても良い様子。

### CSS Modules を使って、Layout コンポーネントを装飾

`components/layout.module.css` ファイルを作成し、以下の記述。

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

Layout コンポーネントに適用する

```jsx
import styles from "./layout.module.css";

function Layout({ children }) {
  return <div className={styles.container}>{children}</div>;
}

export default Layout;
```

### `_app.js` を使って Global Styles を作成する

`_app.js` を `pages` 配下に作成.

```jsx
export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

`styles/global.css` を作成。

```css
html,
body {
  background-color: #999;
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen, Ubuntu,
    Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  line-height: 1.6;
  font-size: 18px;
}

* {
  box-sizing: border-box;
}

a {
  color: #0070f3;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

img {
  max-width: 100%;
  display: block;
}
```

`_app.js` に以下を追加

```jsx
import "../styles/global.css";
```

これで全体に適用するスタイルを作成できる。

### ページの共通レイアウトおよびトップページを更新し、飾り付ける

プロフィール画像を出したりなんだりをまとめてやっていく。

`components/layout.module.css` を更新する

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}

.header {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.headerImage {
  width: 6rem;
  height: 6rem;
}

.headerHomeImage {
  width: 8rem;
  height: 8rem;
}

.backToHome {
  margin: 3rem 0 0;
}
```

`styles/utils.module.css` を作成する

```css
.heading2Xl {
  font-size: 2.5rem;
  line-height: 1.2;
  font-weight: 800;
  letter-spacing: -0.05rem;
  margin: 1rem 0;
}

.headingXl {
  font-size: 2rem;
  line-height: 1.3;
  font-weight: 800;
  letter-spacing: -0.05rem;
  margin: 1rem 0;
}

.headingLg {
  font-size: 1.5rem;
  line-height: 1.4;
  margin: 1rem 0;
}

.headingMd {
  font-size: 1.2rem;
  line-height: 1.5;
}

.borderCircle {
  border-radius: 9999px;
}

.colorInherit {
  color: inherit;
}

.padding1px {
  padding-top: 1px;
}

.list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.listItem {
  margin: 0 0 1.25rem;
}

.lightText {
  color: #999;
}
```

`components/layout.js` を更新する

```jsx
import Head from "next/head";
import styles from "./layout.module.css";
import utilStyles from "../styles/utils.module.css";
import Link from "next/link";

const name = "Your Name";
export const siteTitle = "Next.js Sample Website";

export default function Layout({ children, home }) {
  return (
    <div className={styles.container}>
      <Head>
        <link rel="icon" href="/favicon.ico" />
        <meta
          name="description"
          content="Learn how to build a personal website using Next.js"
        />
        <meta
          property="og:image"
          content={`https://og-image.now.sh/${encodeURI(
            siteTitle
          )}.png?theme=light&md=0&fontSize=75px&images=https%3A%2F%2Fassets.vercel.com%2Fimage%2Fupload%2Ffront%2Fassets%2Fdesign%2Fnextjs-black-logo.svg`}
        />
        <meta name="og:title" content={siteTitle} />
        <meta name="twitter:card" content="summary_large_image" />
      </Head>
      <header className={styles.header}>
        {home ? (
          <>
            <img
              src="/images/profile.jpg"
              className={`${styles.headerHomeImage} ${utilStyles.borderCircle}`}
              alt={name}
            />
            <h1 className={utilStyles.heading2Xl}>{name}</h1>
          </>
        ) : (
          <>
            <Link href="/">
              <a>
                <img
                  src="/images/profile.jpg"
                  className={`${styles.headerImage} ${utilStyles.borderCircle}`}
                  alt={name}
                />
              </a>
            </Link>
            <h2 className={utilStyles.headingLg}>
              <Link href="/">
                <a className={utilStyles.colorInherit}>{name}</a>
              </Link>
            </h2>
          </>
        )}
      </header>
      <main>{children}</main>
      {!home && (
        <div className={styles.backToHome}>
          <Link href="/">
            <a>← Back to home</a>
          </Link>
        </div>
      )}
    </div>
  );
}
```

`pages/layout.js` を更新する

```jsx
import Head from "next/head";
import Layout, { siteTitle } from "../components/layout";
import utilStyles from "../styles/utils.module.css";

export default function Home() {
  return (
    <Layout home>
      <Head>
        <title>{siteTitle}</title>
      </Head>
      <section className={utilStyles.headingMd}>
        <p>[Your Self Introduction]</p>
        <p>
          (This is a sample website - you’ll be building a site like this on{" "}
          <a href="https://nextjs.org/learn">our Next.js tutorial</a>.)
        </p>
      </section>
    </Layout>
  );
}
```

アプリを起動して http://localhost:3000/ を表示するとトップページがきれいにレイアウトされているはず！

### Styling Tips

以下のようなの紹介されている

- `classnames` というライブラリが便利
- `PostCSS` の構成のカスタマイズ
- `Sass` の使い方

## お題４ 事前描画や他所からのデータ取得についてやっていく

https://nextjs.org/learn/basics/data-fetching

### 事前描画について

デフォルトでは、Next.js はすべてのページを事前描画しようとする。

事前描画のメリット

- パフォーマンスに良い
  - クライアントには必要最低限の JavaScript コードがロードされる。
- SEO に良い
  - JavaScript を解釈しなくても、ページの内容が展開されるため
- Next.js に上手く乗っかれれば JavaScript がオフでも動くアプリにできる。

ユーザーが操作により、必要最低限の JavaScript コードがロードされ、処理される。 hydration と呼ぶみたい。

事前描画が機能していれば、JavaScript をオフにしていても、基本的な画面遷移などの操作はサポートされる。

`Ctrl + Shift + I` で Chrome Dev Tool 出して `Ctrl + Shift + P` で Command Menu が出せて、そこから `Disable JavaScript` とか入れると簡単にオフにできる。

なお、いま開発中のものでも試せるが JavaScript をオフにすると CSS のロードが上手く機能しないので、画面が崩れるとのこと。

### Next.js の提供する事前描画の２つの手法

- Static Generation
  - ビルド時に HTML を生成する方法。HTML は各リクエストで再利用される。
  - `npm run dev` などの開発モードでサーバーを起動した場合は `Static Generation` を使用していても各リクエストごとに HTML が生成される。
  - ビルド後は単なる HTML なので CDN で配信できる。高速！
  - 特に理由がなければこっちを使うべき。
  - 一般的に次のようなページで使えます。
    - マーケンティングページ
    - ブログの記事
    - EC サイトの商品ページ
    - ヘルプやドキュメントページ
- Server-side Rendering
  - 各リクエストごとにサーバーサイドで HTML を生成する。
  - ユーザーのリクエストに応じて、画面内容が変化するようなページはこっちを使うべき

Next.js では 事前描画の設定をページごとに行うことができます。描画内容がユーザーごとに変わらないところは `Static Generation` を使用し、 動的なところは `Server-side Rendering` を使うことができる。

### データを使う／使わない `Static Generation`

`Static Generation` では外部のデータを利用して HTML を生成する方法と、しないで HTML 生成する方法があります。

外部データを利用して HTML を生成する方法では `getStaticProps` というものを使って 外部の API や データベースや、ファイルシステムにアクセスすることができる。

外部データを利用した HTML 生成は、ページコンポーネントを `export` するときに `getStaticProps` というメソッドを実装し、これも `export` することで機能する。

この `getStaticProps` はプロダクション向けのビルドを走らせると呼び出され、この結果を使って、動的なページが生成される。

以下の感じ。

```jsx
export default function Home(props) { ... }

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.
  const data = ...

  // The value of the `props` key will be
  //  passed to the `Home` component
  return {
    props: ...
  }
}
```

開発モードでは、リクエストごとに `getStaticProps` が呼び出され利用される。

### Blog 記事を Static Generation で生成するようにする

Blog 記事のもととなる Markdown ファイルを `posts` ディレクトリに配置する

今回は例として `pre-rendering.md` `ssg-ssr.md` を作成。

中身は以下の感じ。

`pre-rendering.md`

```
---
title: 'Two Forms of Pre-rendering'
date: '2020-01-01'
---

Next.js has two forms of pre-rendering: **Static Generation** and **Server-side Rendering**. The difference is in **when** it generates the HTML for a page.

- **Static Generation** is the pre-rendering method that generates the HTML at **build time**. The pre-rendered HTML is then _reused_ on each request.
- **Server-side Rendering** is the pre-rendering method that generates the HTML on **each request**.

Importantly, Next.js lets you **choose** which pre-rendering form to use for each page. You can create a "hybrid" Next.js app by using Static Generation for most pages and using Server-side Rendering for others.
```

`ssg-ssr.md`

```
---
title: 'When to Use Static Generation v.s. Server-side Rendering'
date: '2020-01-02'
---

We recommend using **Static Generation** (with and without data) whenever possible because your page can be built once and served by CDN, which makes it much faster than having a server render the page on every request.

You can use Static Generation for many types of pages, including:

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

You should ask yourself: "Can I pre-render this page **ahead** of a user's request?" If the answer is yes, then you should choose Static Generation.

On the other hand, Static Generation is **not** a good idea if you cannot pre-render a page ahead of a user's request. Maybe your page shows frequently updated data, and the page content changes on every request.

In that case, you can use **Server-Side Rendering**. It will be slower, but the pre-rendered page will always be up-to-date. Or you can skip pre-rendering and use client-side JavaScript to populate data.
```

一般的な Markdown だが先頭の `title` と `date` はメタデータとして今回使用する `gray-matter` というライブラリで利用される。

#### `getStaticProps` を実装していきます

`pages/index.js` に以下のようなことをする `getStaticProps` メソッドを追加します。

- Markdown を読み込み、 `title` と `date` を読み取る。
- 日付で並び替えて、index ページに表示する

Markdown のメタデータを解析するために `gray-matter` をインストールする

```
$ npm install gray-matter
```

プロジェクトルートディレクトリに `lib` というディレクトリを作り `posts.js` というファイルを以下のように作成する。

内容的には、上記で説明した `pages/index.js` の `getStaticProps` でやりたいことをやってくれるユーティリティの様子。

```js
import fs from "fs";
import path from "path";
import matter from "gray-matter";

const postsDirectory = path.join(process.cwd(), "posts");

export function getSortedPostsData() {
  // Get file names under /posts
  const fileNames = fs.readdirSync(postsDirectory);
  const allPostsData = fileNames.map((fileName) => {
    // Remove ".md" from file name to get id
    const id = fileName.replace(/\.md$/, "");

    // Read markdown file as string
    const fullPath = path.join(postsDirectory, fileName);
    const fileContents = fs.readFileSync(fullPath, "utf8");

    // Use gray-matter to parse the post metadata section
    const matterResult = matter(fileContents);

    // Combine the data with the id
    return {
      id,
      ...matterResult.data,
    };
  });
  // Sort posts by date
  return allPostsData.sort((a, b) => {
    if (a.date < b.date) {
      return 1;
    } else {
      return -1;
    }
  });
}
```

`pages/index.js` でこれを利用するようにする。

```js
import { getSortedPostsData } from "../lib/posts";
```

の追加と `getStaticProps` の追加

```js
export async function getStaticProps() {
  const allPostsData = getSortedPostsData();
  return {
    props: {
      allPostsData,
    },
  };
}
```

Home コンポーネント自体もこの props を使うように修正していきます。（`Layout` の中の一番下に新しい section を追加）

```jsx
export default function Home({ allPostsData }) {
  return (
    <Layout home>
      ・・・・
      <section className={`${utilStyles.headingMd} ${utilStyles.padding1px}`}>
        <h2 className={utilStyles.headingLg}>Blog</h2>
        <ul className={utilStyles.list}>
          {allPostsData.map(({ id, date, title }) => (
            <li className={utilStyles.listItem} key={id}>
              {title}
              <br />
              {id}
              <br />
              {date}
            </li>
          ))}
        </ul>
      </section>
    </Layout>
  );
}
```

上記の例の `getStaticProps` は非同期で実装するため、今回の ファイル I/O のかわりに外部 API へ fetch したり、DB からデータを取得したりができます。

なお `getStaticProps` は page のコンポーネントのみで定義できる。

### リクエストごとにデータを取得する必要がある場合の処理

リクエストごとにデータを取得し、動的なページを作成したい場合、サーバーサイドレンダリングを使うと良い。

`getServerSideProps` というメソッド定義する。

```js
export default function Home(props) { ... }

export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    }
  }
}
```

`getServerSideProps` は `context` というパラメータを取る。 `context` にはリクエストに関する情報などが含まれる。

`getServerSideProps` は `getStaticProps` を使うときに比べ、ページのレスポンスを返すまでの時間が遅くなってしまう点、注意が必要。

以下のような条件に該当する場合は、クライアント側で描画する方法もあります。

- 外部のデータを必要としない画面更新。
- ページが配信されたあと、クライアントから外部データを fetch し、残された領域の描画を行う場合。

ユーザーダッシュボードのような画面は上記のような条件に当てはまりそうです。なぜならユーザーのプライベートで独自の内容を含むページで、SEO 対策なども不要なため。

クライアント側でデータを fetch するのであれば SWR というライブラリも便利なので検討してみてくださいとのこと。キャッシュや再検証、時間間隔での再取得など、様々な機能がサポートされています。

## お題５ 動的なパスへの画面遷移

https://nextjs.org/learn/basics/dynamic-routes

### 外部データに依存したページパスに対応して、静的ページを生成する

今回は `/posts/<id>` のようなパスへアクセスがあったらその `<id>` に紐付いた記事を表示できるようにしたい。

上記のようなページを作成するためには `pages/posts/[id].js` という名前でモジュールを作成します。

```jsx
import Layout from "../../components/layout";

export default function Post() {
  return <Layout>...</Layout>;
}
```

ここに今回は `getStaticPaths` というメソッドを定義します。

```js
export async function getStaticPaths() {
  // Return a list of possible value for id
}
```

最後に `getStaticProps` も定義します。

```js
export async function getStaticProps({ params }) {
  // Fetch necessary data for the blog post using params.id
}
```

このようなファイルを配置しておくことで、動的なパスを特定のページでハンドリングするように定義することができます。

以前作成した `pages/posts/first-post.js` はもう不要なため削除して OK。

### `getStaticPaths` の実装

`lib/posts.js` にメソッドを追加します。ディレクトリのファイル群から id のリストを生成する処理を行う。

```js
export function getAllPostIds() {
  const fileNames = fs.readdirSync(postsDirectory);

  // Returns an array that looks like this:
  // [
  //   {
  //     params: {
  //       id: 'ssg-ssr'
  //     }
  //   },
  //   {
  //     params: {
  //       id: 'pre-rendering'
  //     }
  //   }
  // ]
  return fileNames.map((fileName) => {
    return {
      params: {
        id: fileName.replace(/\.md$/, ""),
      },
    };
  });
}
```

作った メソッド を `pages/posts/[id].js` で import して使用します。

```js
import { getAllPostIds } from "../../lib/posts";

// .....

export async function getStaticPaths() {
  const paths = getAllPostIds();
  return {
    paths,
    fallback: false,
  };
}
```

`getStaticPaths` が返すオブジェクトは、 `paths` には `id` をメンバーとして持つオブジェクトの配列を、`fallback` には一旦 `false` 突っ込んでおく。パスがなかった(404)場合のハンドリングを行う必要があれば、`true` 設定してやるっぽい。

### `getStaticProps` の実装

`lib/posts.js` にまたメソッドを追加する。指定した id のファイルの内容を返す処理を行う。

```js
export function getPostData(id) {
  const fullPath = path.join(postsDirectory, `${id}.md`);
  const fileContents = fs.readFileSync(fullPath, "utf8");

  // Use gray-matter to parse the post metadata section
  const matterResult = matter(fileContents);

  // Combine the data with the id
  return {
    id,
    ...matterResult.data,
  };
}
```

これも `pages/posts/[id].js` で import して使用します。

```js
import { getAllPostIds, getPostData } from "../../lib/posts";

// .....

export async function getStaticProps({ params }) {
  const postData = getPostData(params.id);
  return {
    props: {
      postData,
    },
  };
}
```

最後にコンポーネント自体で、このデータを受け取り、表示するように修正します。

```jsx
export default function Post({ postData }) {
  return (
    <Layout>
      {postData.title}
      <br />
      {postData.id}
      <br />
      {postData.date}
    </Layout>
  );
}
```

### Markdown を描画できるようにしたい

Markdown を HTML に変換するのに remark と remark-html というライブラリを使います。

```sh
$ npm install remark remark-html
```

`lib/posts.js` でこれを import して使用するように `getPostData` を修正します。

```js
import remark from "remark";
import html from "remark-html";

// ....

export async function getPostData(id) {
  const fullPath = path.join(postsDirectory, `${id}.md`);
  const fileContents = fs.readFileSync(fullPath, "utf8");

  // Use gray-matter to parse the post metadata section
  const matterResult = matter(fileContents);

  // Use remark to convert markdown into HTML string
  const processedContent = await remark()
    .use(html)
    .process(matterResult.content);
  const contentHtml = processedContent.toString();

  // Combine the data with the id and contentHtml
  return {
    id,
    contentHtml,
    ...matterResult.data,
  };
}
```

今回メソッドを Promise を返すように修正したため、呼び出し元も変更する。

```js
export async function getStaticProps({ params }) {
  // Add the "await" keyword like this:
  const postData = await getPostData(params.id);
  // ...
}
```

### 記事のページをきれいにする

タイトルを入れたり

```jsx
import Head from "next/head";

export default function Post({ postData }) {
  return (
    <Layout>
      <Head>
        <title>{postData.title}</title>
      </Head>
      ...
    </Layout>
  );
}
```

日付を整形したり。。整形のために `date-fns` というライブラリを使用します。

```sh
$ npm install date-fns
```

日付表示用のコンポーネント `components/date.js` の追加

```jsx
import { parseISO, format } from "date-fns";

export default function Date({ dateString }) {
  const date = parseISO(dateString);
  return <time dateTime={dateString}>{format(date, "LLLL d, yyyy")}</time>;
}
```

その他、スタイルをあてて、最終的には以下の感じ。

```jsx
import utilStyles from "../../styles/utils.module.css";

export default function Post({ postData }) {
  return (
    <Layout>
      <Head>
        <title>{postData.title}</title>
      </Head>
      <article>
        <h1 className={utilStyles.headingXl}>{postData.title}</h1>
        <div className={utilStyles.lightText}>
          <Date dateString={postData.date} />
        </div>
        <div dangerouslySetInnerHTML={{ __html: postData.contentHtml }} />
      </article>
    </Layout>
  );
}
```

### Index ページからのリンクを追加する。

`pages/index.js` の `<li>` 要素 を以下の通り更新する。

```jsx
import Link from "next/link";
import Date from "../components/date";

// ....

<li className={utilStyles.listItem} key={id}>
  <Link href="/posts/[id]" as={`/posts/${id}`}>
    <a>{title}</a>
  </Link>
  <br />
  <small className={utilStyles.lightText}>
    <Date dateString={date} />
  </small>
</li>;
// ....
```

## お題６ API の追加

https://nextjs.org/learn/basics/api-routes

### API となるファイルを追加

`hello.js` を `pages/api` 配下。

```js
// req = request data, res = response data
export default (req, res) => {
  res.status(200).json({ text: "Hello" });
};
```

このように追加すれば `http://localhost:3000/api/hello` のアドレスでアクセス可能。

注意点として内部で作成した API には `getStaticProps` や `getStaticPaths` からはアクセスは NG とのこと。

また `pages/api` 配下に設置したコードはクライアント側には配信されないコードとなるため、秘匿にしたいコードを記述しても良いとのこと。

そして API もページ同様、動的なルートも使用可能とのこと。

### 一通り終わったので。

一旦ここで終わり。
