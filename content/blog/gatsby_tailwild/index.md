---
title: GatsbyにTailwild CSSを導入
date: "2021-10-16"
pagetype: ['gatsby']
hero: aws_console.jpg
description: "Install Tailwild CSS with Gatsby"
---
# CSS周りについて
※私はフロントエンド周りについて、まったくと言っていいほど知識がないので、もし間違ったことを記載していたら申し訳ないです。

このブログはGatsbyを使用して記載しており、starterの[`gatsby-starter-blog`](https://www.gatsbyjs.com/starters/gatsbyjs/gatsby-starter-blog)
を使用している。

ブログを書き始めるに当たって、CSSを追加していくうちに、CSS内でクラスがコンフリクトするなどの記載があり管理方法を考えていた。
その問題を解決できそうなのが、[Tailwild CSS](https://tailwindcss.jp/)だった。

<!-- https://www.gatsbyjs.com/starters/ -->

# [Tailwild CSS](https://tailwindcss.jp/)
>Tailwind CSSは高度にカスタマイズ可能な低レベルのCSSフレームワークであり、 オーバーライドするために戦わなければならない煩わしい独善的なスタイルなしに、 オーダーメイドのデザインを構築するために必要なすべてのビルディングブロックを提供します。

CSSにクラスを作成しなくても、Tailwild CSSが提供するコンポーネントを組み合わせるだけで、簡単にデザインすることができるようにすることができるフレームワークという認識です。ごちゃごちゃしなくなるからめちゃ便利！！
早速導入！！！

# 導入
GatsbyへTailwildを導入するのになかなか苦戦したので、下記に私の場合、上手く行った導入手順を示します。
(導入がすんなりいかなかったのはOSがwindowsであったためである可能性がある)

## 環境
OS : Windows 10
npm : 6.14.15
node.js : v14.18.0
gatsby : 3.14.1

## Tailwind CSSのインストール
基本的に公式の導入方法と同じ
https://tailwindcss.com/docs/guides/gatsby
```
npm install -D gatsby-plugin-postcss tailwindcss@latest postcss@latest autoprefixer@latest
```

## Tailwind.config.jsファイルの作成
```
npx tailwindcss init -p
```
このコマンド実行後、root直下にtailwind.config.jsが作成されていることを確認。
また`postcss.config.js`をtailwind.confg.jsと同じ階層に作成する。
`postcss.config.js`には下記の内容を記載する。
```
// postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## Tailwind.config.js内のpurgeを変更
build時にTailwind CSSのために割く容量が大きくなってしまうため、どこのファイルにTailwind CSSを適用するか指定する。(defaultは作業ディレクトリ全て)
```
// tailwind.config.js
  module.exports = {
   purge: ['./src/**/*.{js,jsx,ts,tsx}'], //変更箇所
    darkMode: false, // or 'media' or 'class'
    theme: {
      extend: {},
    },
    variants: {
      extend: {},
    },
    plugins: [],
  }
```

## gatsby-config.jsへ反映
gatsbyはpluginを導入する際には、gatsby-config.jsへ記載が必要であるため追記。
```
  module.exports = {
   plugins: ['gatsby-plugin-postcss'],
  }
```

## CSSファイルに記載
Tailwild CSSを使えるようにCSSファイルを作成しimportする。
ここで詰まった。

公式では以下のように記載するように書かれている。
```
/* ./src/styles/global.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

私の環境では、この記載方法だと参照されなかった。
動いたのは下記の記載方法

```
@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities";
```

また、下記の記載方法でないと動かない方もいるみたい。
https://qiita.com/masakinihirota/items/bd8c07aa54efad307588

```
/* Windows10の場合？ */
@import "~tailwindcss/base";
@import "~tailwindcss/components";
@import "~tailwindcss/utilities";
```

## 全体へのスタイルの適用
`gatsby-browser.js`へ先ほど記載したCSSファイルをimportする。

```
// ./gatsby-browser.js
 import './src/styles/global.css';
```
## テスト
/src/pages/配下に`test.js`などテキトーにファイルを作り、ファイルへ下記の内容を記載

```
import React from "react"

const Test = () => {
  return (
    <div>
      <h1 class="text-xl text-red-500">Welcome to Tailwind CSS!</h1>
    </div>
  )
}
export default Test
```
`gatsby develop`し、localhost:8000/testへ移動し、下記のように赤字になっていれば成功。

![イメージ図](2021-10-16-16-34-47.png)

## 最後に
Tailwind CSSの導入がすんなり行かなかったため、覚え書きとして手順を書きました。
誰かのお力になれば幸いです。
