---
title: "1日目"
weight: 1
bookToc: true
---

# 1日目

## アプリケーションの要件

では、早速アジャイル開発で実際に動くアプリケーションを作ってみましょう。
今回は、簡単な例としてタスク管理のアプリを作ってみます。
こういったハンズオンでなにをもってアジャイル開発と言うかは難しいところなのですが、
事前にかっちりとした設計はせずに都度必要な機能を継ぎ足していく形で進めます。
都度、新しい機能を追加するStoryが追加されているという体で読み進めてください。


## アーキテクチャと開発言語、フレームワークの決定

実は、一番難しいのがプロジェクト立ち上げのときだったりします。
アジャイル開発ではあらゆる変更を受け入れるようにSOLID原則などのプログラミング技法に則って開発をすすめます。
しかし、それでも開発を進めてからでは変更が難しいものがいくつかあります。

一つは開発言語、そしてもう一つはフレームワークです。

この２つは後から変更することが非常に困難であり、プロジェクトの特性を見極めて慎重に決定する必要があります。
最初を丁寧にやればやるほど後から楽になります。最初の検討を怠ってはいけません。

{{< hint info >}}

**開発言語変更の例**

Javascriptとそのスーパーセットである、Typescriptはプロジェクト内で共存が可能で、
順次置き換えていくと言った戦略が取れるでしょう。
また、JavaとKotlinも順次置き換える戦略がとることができ、
現に筆者のプロジェクトでは当初JavascriptとJavaを採用していましたが、
開発に着手して１ヶ月半ほどでTypescriptとKotlinに変更しました。
しかし、これはまだ着手してまもなく規模もそこまで大きくなかったため踏み切れたものです。

ソフトウェアの開発規模が大きくなるにつれ次第に変更は難しくなります。
最初の開発言語は慎重にやるべきです。
{{< /hint >}}

## プロジェクトの構成

今回は簡単なタスク管理アプリを作ってみます。
これから作るのはフロントエンドはReactで開発言語はTypescript、バックエンドはSpring Bootで開発言語はKotlinで、
フロントエンドとバックエンドがHTTPでJSONをやり取りして、画面を描画する極々シンプルなSPAアプリケーションです。

![アーキテクチャ](arch.jpg)

## クライアントの環境構築

{{< hint warning >}}

**ハンズオンを始めるにあたって**

ハンズオン形式で細かくソースコードを提示しながらアプリケーションを作っていきますが、
それをただ写すだけでは意味がありませんし、それがあなたの身につくことはありません。
ましてや、眺めているだけでは決してあなたの身につくことないでしょう。

このサンプルアプリケーションではSpring Framework(Spring Boot)や、
React.jsその他多くのOSSを使用していますが、わからないことを都度 **各ライブラリやフレームワークの公式のリファレンスや [MDN](https://developer.mozilla.org/ja/docs/Web)** を読み、
理解するよう努めることがとても大事です。

**公式のリファレンス以上に参考になるドキュメントはWeb上のどこにもない** ということを肝に銘じ、
公式のリファレンスを読めるようになりましょう。

コピペでしかソースコードを書けないエンジニアの特徴に **エラーメッセージを読もうとしない**、**公式のドキュメントを読もうとしない**、**英語のドキュメントを読もうとしない**というのがあります。
そうならないようにしましょう。

{{< /hint >}}

### reactアプリの作成

```shell
npm create vite@latest todo-app-client -- --template react-ts
```

今回はtypescriptのtemplateから作成を始めます。
[vite](https://vite.dev/guide/)を使ってクライアントの雛形を作りました。

このTemplateには不要な初期実装が含まれてますので、ひとまずmain.tsxとindex.tsx、vite-env.d.ts以外のファイルを削除しました。
（削除したファイルを参照している箇所も削除してください。）

```
src
├── App.tsx
├── main.tsx
└── vite-env.d.ts
```

### eslintの設定

一番最初にやるべきはlintの設定です。lintとはコードが規約に準じているかを確認してくれるライブラリです。

```shell
npm install --save-dev eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
npx eslint --init
```

npx eslint --initコマンドを叩くと設定ウィザードがでてきますので、以下に従って実行します。

```
onebase@Onebase-Maguro todo-app-client % npx eslint --init            
✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · Yes
✔ Where does your code run? · browser
✔ Would you like to install them now? · Yes
✔ Which package manager do you use? · npm
```

eslintに必要なパッケージが導入されます。　eslintの設定ファイルはjavascriptでもYAMLでもどっちでもいいのですが、
今回はjavascriptにします。

eslint --initを実行するとeslint.config.jsが出力されますが、少し手を加えます。

```Javascript
// eslint.config.js
import globals from "globals";
import pluginJs from "@eslint/js";
import tseslint from "typescript-eslint";
import pluginReact from "eslint-plugin-react";
import pluginReactJSXRuntime from "eslint-plugin-react/configs/jsx-runtime.js"; // 追加


/** @type {import('eslint').Linter.Config[]} */
export default [
  {files: ["**/*.{js,mjs,cjs,ts,jsx,tsx}"]},
  {languageOptions: { globals: globals.browser }},
  pluginJs.configs.recommended,
  ...tseslint.configs.recommended,
  pluginReact.configs.flat.recommended,
  pluginReactJSXRuntime // 追加
];
```

さらにpackage.jsonにlintのscriptを追記します。
```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "lint": "eslint 'src/**/*.{js,jsx,ts,tsx}'",
    "preview": "vite preview"
  }
}
```

この時点で`npm run lint`を実行すると様々なエラーが出力されるはずです。

```bash
onebase@Onebase-Maguro todo-app-client % npm run lint

> todo-app-client@0.1.0 lint
> eslint --ext .ts,.tsx ./src


/Users/onebase/IdeaProjects/todo-app-client/src/App.tsx
  1:8   error  'React' was used before it was defined          no-use-before-define
  5:5   error  JSX not allowed in files with extension '.tsx'  react/jsx-filename-extension
  5:25  error  A space is required before closing bracket      react/jsx-tag-spacing

/Users/onebase/IdeaProjects/todo-app-client/src/index.tsx
   1:8   error  'React' was used before it was defined                      no-use-before-define
   3:17  error  Unable to resolve path to module './App'                    import/no-unresolved
   3:17  error  Missing file extension for "./App"                          import/extensions
   4:23  error  Unable to resolve path to module './stores/store'           import/no-unresolved
   4:23  error  Missing file extension for "./stores/store"                 import/extensions
   8:3   error  JSX not allowed in files with extension '.tsx'              react/jsx-filename-extension
  13:34  error  Missing trailing comma                                      comma-dangle
  15:1   error  Too many blank lines at the end of file. Max of 0 allowed   no-multiple-empty-lines

/Users/onebase/IdeaProjects/todo-app-client/src/stores/hooks.ts
  2:45  error  Unable to resolve path to module './store'  import/no-unresolved
  2:45  error  Missing file extension for "./store"        import/extensions

✖ 14 problems (14 errors, 0 warnings)
  4 errors and 0 warnings potentially fixable with the `--fix` option.

```


### prettierの設定

指摘するだけですと不便ですので、自動で修正してくれるようにprettierを導入します。
まず、eslintからprettierの競合設定を外す拡張を導入します。

```bash
npm i --save-dev eslint-config-prettier
```

```javascript
extends: [
    'plugin:react/recommended',
    'airbnb',
    'prettier'  // 追記
],
```

.eslintrc.jsのextendsにprettierの設定をいれました。
次にprettierを導入します。

```bash
npm install --save-dev prettier
```

プロジェクトルートディレクトリ配下に`.prettierrc`ファイルを作成します。

```
// .prettierrc
{
    "singleQuote": true,
    "tabWidth": 2,
    "semi": false,
    "bracketSpacing": false,
    "jsxBracketSameLine": true
}
```

package.jsonのscriptに修正用のコマンドも入れてしまいましょう。

```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "lint": "eslint --ext .ts,.tsx ./src",
    "fix": "npm run format && npm run lint:fix",
    "format": "prettier --write 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:fix": "eslint --fix 'src/**/*.{js,jsx,ts,tsx}'"
  }
}
```

これで設定は完了です。`npm run fix`を実行してみましょう。

```

/Users/onebase/IdeaProjects/todo-app-client/src/App.tsx
  2:18  error  Unexpected use of file extension "svg" for "./logo.svg"  import/extensions
  2:18  error  Unable to resolve path to module './logo.svg'            import/no-unresolved
  3:8   error  Unable to resolve path to module './App.css'             import/no-unresolved

/Users/onebase/IdeaProjects/todo-app-client/src/index.tsx
  3:8  error  Unable to resolve path to module './index.css'  import/no-unresolved

```

ほとんどのエラーがprettierにより修正され、エラーが4つだけ出力されました。 これはApp.tsxとindex.tsxの不要なimport文や不要なlinkを削除すると解消されます。


ここまでのソースコードは [https://github.com/Onebase-Fujitsu/todo-app-client/tree/step1](https://github.com/Onebase-Fujitsu/todo-app-client/tree/step1) においてあります。

### css in JSの設定

次にスタイルシートの記述方法を設定します。これも後から変更するのは大変ですので慎重に決定する必要があります。
有名どころはこの辺になるかと思います。

- Material UI
- Tailwind CSS
- styled-components
- Chakra UI

Material UIはスタイル適用済みのUI Componentを提供するもので、Tailwind CSSやstyled-components、Chakra UIはJSXファイルの中でスタイルを規定していくものです。
Material UIはコンポーネントが提供されているため、さっと見た目のいいアプリを作るのにはいいですが、
いかにもな"Material UI"感がでてしまうのが難点です。

Tailwind CSSやstyled-componentsなどはその点自由にデザインを組めますが、
両者でコーディングスタイルは大きく異なっています。

今回はstyled-componentsを導入してみます。

{{< hint info >}}

**styled-componentsの利点**

styled-componentsで定義するスタイルはReactのコンポーネントそのものです。このため、styled-componentsを利用することで、componentとstyleのマッピングが無くなります。

さらに、ローカルスコープで利用できるため、複数のコンポーネントで同一の名前が使用でき、長い命名に悩まされる必要がなくなります。

また、styled-componentsを使用すると、CSSコードを記述してコンポーネントのスタイルを設定できるため、他のCSSフレームワークに比べて学習コストが低いことも利点の一つです。

{{< /hint >}}

styled-componentsの導入方法は[こちら](https://styled-components.com/)に書いてあるので、これに習って導入してみましょう。

styled-componentsの導入は非常に簡単で以下のコマンドを実行するだけです。

```shell
npm install --save styled-components @types/styled-components
```

これでstyled-components導入ができました。
正しく導入できているか確認してみましょう。

App.tsxを以下のように変更してコンパイルしてみましょう。

```javascript
// App.tsx
import styled from 'styled-components'

const Div = styled.div`
    color: red;
`

function App() { 
  return <Div>test</Div>
}

export default App
```

```shell
npm run dev
```

![tailwind](tailwind.jpg)

ブラウザで赤い文字でtestと表示されているのが確認できたら正しく導入できていますので、確認できたらApp.tsxをもとに戻しておいてください。

ここまでのソースは [https://github.com/Onebase-Fujitsu/todo-app-client/tree/step2](https://github.com/Onebase-Fujitsu/todo-app-client/tree/step2) に置いてあります。

### テスト環境の整備

次にクライアントのテスト環境を整備していきます。
ReactのTestライブラリとして著名なものにenzymeとReact Testing Libraryがあります。
どちらを使ってもいいのですが、enzymeはreact16までしか現在対応していないため、
今回はReact Testing Libraryを採用することにします。
React Testing Libraryはcreate-react-app時に一緒に導入されているため、個別のインストールや設定作業は不要で使用できます。

次にimportでDevDependenciesのライブラリを読み込んでいるためeslintがエラーを出してしまいますので、これはoffにしてしまいます。

```javascript
// .eslintrc.js
module.exports = {
    // 中略
  rules: {
    'import/extensions': [
      'error',
      {
        js: 'never',
        jsx: 'never',
        ts: 'never',
        tsx: 'never',
      }
    ],
    'react/jsx-filename-extension': [
      'error',
      {
        extensions: ['.jsx', '.tsx']
      }
    ],
    'react/react-in-jsx-scope': 'off',
    'import/prefer-default-export': 'off',
    'import/no-extraneous-dependencies': 'off', // 追加
  },
};
```

create-react-appは標準でテストランナーにJestを採用しているのですが、このままではeslintがJestの処理に対してエラーを出すため、これも修正していきます。
まず、eslint-plugin-jestを導入。

```shell
npm install --save-dev eslint-plugin-jest
```

そして、.eslintrc.jsに以下の二行を追加します。

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
    "jest/globals": true,       // 追記
  },
  // 中略
  plugins: [
    'react',
    '@typescript-eslint',
    "jest"                      // 追記
  ]
}
```

この状態で`npm run test`を実行してみましょう。テストランナーが立ち上がり、src配下のファイルの変更を監視しはじめます。
ファイルの変更があるたびにテストを実行してくれるようになります。

Jestはsrcディレクトリに直下に`__tests__`ディレクトリを作るか、
どこでもいいので`*.test.ts`という形式でテストファイルを作ると自動でテストが動きます。

どちらでもいいのですが、今回は前者の`__tests__`ディレクトリを作成する形式でいきます。

## ヘッダーの作成

### テストの作成

src直下に__tests__ディレクトリを作成し、その配下にcomponentsディレクトリを作成して、その配下にHeader.test.tsxを作りましょう。

```
src
├── App.tsx
├── __tests__
│   └── components
│       └── Header.test.tsx  // 新規作成
│── index.tsx
└── setupTests.ts

```

```typescript jsx
// Header.test.tsx
import {cleanup, render, screen} from "@testing-library/react";

describe("Header", () => {
  afterEach(() => {
    cleanup()
  })

  it("ヘッダーの初期表示", () => {
    render(<Header />)
    expect(screen.getByText('Todo App')).toBeInTheDocument()
  })
})
```

最初のテストはこのようにしてみました。h1要素があることを確認しています。

この時点ではHeaderコンポーネントは作成されていないので、当然失敗します。
npm run testを実行するとこのような表示になっているはずです。

```
FAIL  src/__tests__/components/Header.test.tsx
Header
✕ ヘッダーの初期表示 (1 ms)

● Header › ヘッダーの初期表示

    ReferenceError: Header is not defined

       7 |
       8 |   it("ヘッダーの初期表示", () => {
    >  9 |     render(<Header />)
         |             ^
      10 |     expect(screen.getByText('Todo App')).toBeInTheDocument()
      11 |   })
      12 | })

      at Object.<anonymous> (src/__tests__/components/Header.test.tsx:5:30)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.358 s, estimated 1 s
```


### Headerの実装

src配下にcomponentsディレクトリを作成し、その配下にHeader.tsxを作成します。

```typescript jsx
// Header.tsx
const Header = () => (
  <h1>Todo App</h1>
)

export default Header
```

Header.tsxを作ったら、Header.test.tsxを開き、先程作ったHeaderコンポーネントをインポートしてみましょう。

```typescript jsx
import {cleanup, render, screen} from "@testing-library/react";
import Header from "../../components/Header";

describe("Header", () => {
  afterEach(() => {
    cleanup()
  })

  it("ヘッダーの初期表示", () => {
    render(<Header />)
    expect(screen.getByText('Todo App')).toBeInTheDocument()
  })
})
```

このファイルを保存してテストを実行すると、テストが無事通っていることがわかると思います。
```
Watch Usage: Press w to show more.
 PASS  src/__tests__/components/Header.test.tsx
  Header
    ✓ ヘッダーの初期表示 (6 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.579 s, estimated 1 s

```
最初のテストが通りました！おめでとうございます。

## 画面の表示

Headerは作りましたがまだReactアプリに組み込んでいないため、まだブラウザ上ではなにも表示されていません。
次に画面に表示するようにしてみましょう。

コンポーネントのときと同じように__tests__配下にpagesディレクトリを作成し、`Home.test.tsx`を作成します。
同様にsrc/pages配下に`Home.tsx`を作成しましょう。

```
src
├── App.tsx
├── __tests__
│     ├── components
│     │     └── Header.test.tsx
│     └── pages
│         └── Home.test.tsx
├── components
│     └── Header.tsx
├── index.css
├── index.tsx
├── pages
│     └── Home.tsx
└── setupTests.ts
```

```typescript jsx
// Home.test.tsx
import {cleanup, render, screen} from "@testing-library/react";
import Home from "../../pages/Home";

describe("Home画面", () => {

  afterEach(() => {
    cleanup()
  })

  it("ホーム画面の初期表示", () => {
    render(<Home />)
    expect(screen.getByText("Todo App")).toBeInTheDocument()
  })
})
```

```typescript jsx
// Home.tsx
import Header from "../components/Header";

const Home = () => (
  <div>
    <Header/>
  </div>
)

export default Home
```

Home.test.tsxと、Home.tsxはそれぞれこのようにしてみました。
先程のテストとは違ってHeaderコンポーネントを表示していることを確認するテストになっています。
これも問題なく通ると思います。

この状態でApp.tsxを以下のように変更してみましょう。

```typescript jsx
// App.tsx
import Home from "./pages/Home";

const App = () => (
      <div className="App">
        <Home />
      </div>
)

export default App
```

このままではeslintがfunctionの定義方法についてエラーを出すため、これも修正します。

```typescript jsx
// .eslintrc.js
module.exports = {
    // 中略
  rules: {
    'import/extensions': [
      'error',
      {
        js: 'never',
        jsx: 'never',
        ts: 'never',
        tsx: 'never',
      }
    ],
    'react/jsx-filename-extension': [
      'error',
      {
        extensions: ['.jsx', '.tsx']
      }
    ],
    'react/react-in-jsx-scope': 'off',
    'import/prefer-default-export': 'off',
    'import/no-extraneous-dependencies': 'off',
    'react/function-component-definition': [ //追加ここから
        2,
      {
        namedComponents: 'arrow-function',
      },
    ], //追加ここまで
};
```


`npm run start`を実行して [https://localhost:3000](https://localhost:3000) にアクセスしてみましょう。

![ホーム画面](home_initial.jpg)

TodoAppという文字列が表示されていたら成功です！

## デザインの適用

このままだと寂しいのでHeaderにデザインを適用してみましょう。
styled-componentsを使ってデザインを適用していきます。
styled-componentsの使い方については[こちら](https://styled-components.com/docs/basics#getting-started)を参照してください。

実際にデザインをヘッダーに適用していきましょう。

```typescript jsx
// Header.tsx
import styled from 'styled-components'

const Wrapper = styled.div`
  display: flex;
  align-items: center;
  background-color: #10B981;
  padding: 1.5rem;
`

const Title = styled.h1`
  font-weight: 600;
  font-size: 1.25rem;
  line-height: 1.75rem;
  color: #ffffff;
  letter-spacing: -0.025em;
`

const Header = () =>
  <Wrapper data-testid='Header'>
    <Title>Todo App</Title>
  </Wrapper>

export default Header
```

Header.tsxをこのように変更してみました。

次にReactのデフォルトスタイルを修正します。

src直下にindex.cssを作成して
```css
/* index.css */
body {
    margin: 0;
}
```

index.tsxで読み込むことでデフォルトマージンを削除します。

```typescript jsx
// index.tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import './index.css' //追加
import App from './App'
import reportWebVitals from './reportWebVitals'

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement)
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

以上でスタイルの適用は完了です。

![ヘッダーにデザイン適用](home_apply_design.jpg)

ヘッダーに背景色をつけて、Headerの文字を白に、そして大きくしてみました。
一気に華やかになってきましたね。

ここまでのソースコードは [https://github.com/Onebase-Fujitsu/todo-app-client/tree/step3](https://github.com/Onebase-Fujitsu/todo-app-client/tree/step3) に置いてあります。

{{< hint warning >}}

**styled-components使用時の注意点**

実際にstyled-componentsを使用してお気づきかもしれませんが、styled-componentsは非常に自由度の高いCSSフレームワークです。

そのため、チームメンバーがそれぞれ自由に開発を進めると、可読性が下がりバグの原因に繋がります。
例えばデザインのみを定義したcomponentと、何か機能を持ったcomponentとを一見して見分けることができません。
また、定義したcomponentの全体像がわかりにくくなります。

そのためチームで開発する際には、ディレクトリ構成やファイル構造、命名規則などのルールを定めて使用しましょう。

{{< /hint >}}

---

一日目はクライアントアプリの環境構築と最初の画面表示までいきました。
環境構築はアプリ構築になれている方がやらないとかなり難しい部分だったりします。
というのもアプリの初期構築はそんなに回数こなすものでもないためです。

特にReactは最初の環境構築がcreate-react-appで簡略化されたとはいえ難しいです。
まだReactのフレームワークも触りの部分しか扱っていません。
ここまで、躓くことが無いように頑張ってください。

---

2日目に続きます。

{{< button relref="/docs/practice/day2" >}}2日目{{< /button >}}
