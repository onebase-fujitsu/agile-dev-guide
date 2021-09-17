---
title: "1日目"
weight: 1
bookToc: true
---

# 1日目

## アプリケーションの要件

では、早速アジャイル開発で実際に動くアプリケーションを作ってみましょう。
今回は、簡単な例としてタスク管理のアプリを作ってみます。


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
React.jsやReduxその他多くのOSSを使用していますが、わからないことを都度 **各ライブラリやフレームワークの公式のリファレンスや [MDN](https://developer.mozilla.org/ja/docs/Web)** を読み、
理解するよう努めることがとても大事です。

**公式のリファレンス以上に参考になるドキュメントはWeb上のどこにもない** ということを肝に銘じ、
公式のリファレンスを読めるようになりましょう。

コピペでしかソースコードを書けないエンジニアの特徴に **エラーメッセージを読もうとしない**、**公式のドキュメントを読もうとしない**、**英語のドキュメントを読もうとしない**というのがあります。
そうならないようにしましょう。

{{< /hint >}}

### reactアプリの作成

```shell
npx create-react-app todo-app-client --template redux-typescript --use-npm
```

今回はredux-typescriptのtemplateから作成を始めます。
`create-react-app`を使ってクライアントの雛形を作りました。

このTemplateには不要な初期実装が含まれてますので、ひとまずApp.cssや`features/counter`配下など諸々を削除しました。
そして、storesディレクトリと、componentsディレクトリ、pagesディレクトリを作成し、ファイルを以下のように整理しました。

```
src
├── App.tsx
├── components
├── features
├── index.tsx
├── pages
├── react-app-env.d.ts
├── serviceWorker.ts
├── setupTests.ts
└── stores
    ├── hooks.ts
    └── store.ts
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
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · guide
✔ Which style guide do you want to follow? · airbnb
✔ What format do you want your config file to be in? · JavaScript
```

eslintに必要なパッケージが導入されます。　eslintの設定ファイルはjavascriptでもYAMLでもどっちでもいいのですが、
今回はjavascriptにします。

eslint --initを実行するとeslintrc.jsが出力されますが、少し手を加えます。

```Javascript
//.eslintrc.js
module.exports = {
    env: {
        browser: true,
        es2021: true,
    },
    extends: [
        'plugin:react/recommended',
        'airbnb',
    ],
    parser: '@typescript-eslint/parser',
    parserOptions: {
        ecmaFeatures: {
            jsx: true,
        },
        ecmaVersion: 12,
        sourceType: 'module',
        tsconfigRootDir: __dirname,
        project: ['./tsconfig.json'],
    },
    plugins: [
        'react',
        '@typescript-eslint',
    ],
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
    },
    settings: {
        'import/resolver': {
            node: {
                paths: ['src'],
                extensions: ['.js', '.jsx', '.ts', '.tsx']
            }
        }
    }
};
```

さらにpackage.jsonにlintのscriptを追記します。
```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "lint": "eslint --ext .ts,.tsx ./src"
  }
}
```

この時点でnpm run lintを実行すると様々なエラーが出力されるはずです。

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
   5:1   error  `react-redux` import should occur before import of `./App`  import/order
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
/Users/onebase/IdeaProjects/todo-app/client/src/App.tsx
  1:8  error  'React' was used before it was defined  no-use-before-define

/Users/onebase/IdeaProjects/todo-app/client/src/index.tsx
  1:8  error  'React' was used before it was defined  no-use-before-define

```

ほとんどのエラーがprettierにより修正され、エラーが2つだけ出力されました。
これはApp.tsxとindex.tsxの1行目のReactのimportを削除すると解消されます。

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

個人的にはTailwind CSSを推しているため今回はTailwind CSSを導入してみます。

{{< hint info >}}

**なぜTailwind CSS?**

制約こそデザインシステムだというライブラリの考え方に共感するところが多いからです。
Tailwind CSSは予め用意されたクラス名定義を組み合わせでデザインをしていくわけですが、
その定義から外れたようなデザインがとてもしにくくなっているのが特徴です。

アジャイル開発では最初にデザインシステムがない状態から始めることが一般的です。
その状態において自由度の高すぎるCSS記述はアプリデザインの一貫性の欠如をもたらします。

自由な記述ができないことで、デザインに一貫性を保ち、それがデザインシステムになっていくという思想は、非常にアジャイル的だと筆者は思っています。

styled-componentはTailwind CSSと比較してとにかく自由です。
どんなデザインでも普通のHTML + CSS(SASS)と同じように実装できるでしょう。
そういった部分に価値観を置くのであれば、styled-componentsの採用も考えていいでしょう。
将来自由度の高いデザイン記述が必要になる可能性は無いとは言えません。プロジェクトの特性に合わせて選択しましょう。

{{< /hint >}}

Tailwind CSSの導入方法はこちらに書いてあるので、これに習って導入してみましょう。

[https://tailwindcss.com/docs/guides/create-react-app](https://tailwindcss.com/docs/guides/create-react-app)

tailwind cssのインストールコマンドをまず叩いて、

```shell
npm install -D tailwindcss@npm:@tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9
npm install @craco/craco
```

package.jsonのスクリプトを編集
```
// package.json
"scripts": {
    "start": "craco start",   // 変更
    "build": "craco build",   // 変更
    "test": "craco test",     // 変更
    "eject": "react-scripts eject",
    "lint": "eslint --ext .ts,.tsx ./src",
    "fix": "npm run format && npm run lint:fix",
    "format": "prettier --write 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:fix": "eslint --fix 'src/**/*.{js,jsx,ts,tsx}'"
  },
```

craco.config.jsを新規に作成

```javascript
// craco.config.js
module.exports = {
    style: {
        postcss: {
            plugins: [
                require('tailwindcss'),
                require('autoprefixer'),
            ],
        },
    },
}
```

Tailwind CSSの初期設定コマンドを実行

```shell
npx tailwindcss-cli@latest init
```

tailwind.config.jsが生成されるので、中身を更新

```javascript
// tailwind.config.js
module.exports = {
    purge: ['./src/**/*.{js,jsx,ts,tsx}', './public/index.html'],   // 更新
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

src直下にindex.cssを作成して

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

index.tsxで読み込み

```javascript
import {StrictMode} from 'react'
import ReactDOM from 'react-dom'
import {Provider} from 'react-redux'
import './index.css'  // 追加
import App from './App'
import {store} from './stores/store'

ReactDOM.render(
  <StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </StrictMode>,
  document.getElementById('root')
)
```

これでTailwind CSSの導入ができました。
正しく導入できているか確認してみましょう。

```javascript
// App.tsx
function App() {
    return (
        <div className="text-red-500">test</div>
    )
}

export default App
```

App.tsxを以下のように変更してコンパイルしてみましょう。

```shell
npm run start
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
    // 省略
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
    // 省略
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
├── components
├── features
├── index.css
├── index.tsx
├── pages
├── react-app-env.d.ts
├── setupTests.ts
└── stores
    ├── hooks.ts
    └── store.ts
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
当然この時点ではshallowはenzymeが提供しているレンダリングメソッドの1つで、コンポーネントをshallowRenderingしてくれます。
仮想的に直接Headerコンポーネントだけをレンダリングしていると考えてみてください。

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

src/components配下にHeader.tsxを作成します。

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
├── features
├── index.css
├── index.tsx
├── pages
│     └── Home.tsx
├── react-app-env.d.ts
├── setupTests.ts
└── stores
    ├── hooks.ts
    └── store.ts
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
import Home from "./pages/Home";

function App() {
  return (
    <div className="App">
      <Home />
    </div>
  )
}

export default App
```

`npm run start`を実行して [https://localhost:3000](https://localhost:3000) にアクセスしてみましょう。

![ホーム画面](home_initial.jpg)

TodoAppという文字列が表示されていたら成功です！

## デザインの適用

このままだと寂しいのでHeaderにデザインを適用してみましょう。
Tailwind CSSでは予め定義されたclass名を使ってデザインを適用していきます。
例えばmarginを設定したい場合は該当のDOMに`m-1`といったクラス名を適用したらいいですし、
文字の色を変えたい場合は`text-red-500`のようなクラス名を適用します。
どのようなCSS定義があるかは `https://tailwindcss.com/docs` を参照してください。

実際にデザインをヘッダーに適用していきましょう。

```typescript jsx
// Header.tsx
const Header = () => (
  <div data-testid="Header" className="flex items-center bg-green-500 p-6">
    <h1 className="font-semibold text-xl text-white tracking-tight">Todo App</h1>
  </div>
)

export default Header
```

Header.tsxをこのように変更してみました。

![ヘッダーにデザイン適用](home_apply_design.jpg)

ヘッダーに背景色をつけて、Headerの文字を白に、そして大きくしてみました。
一気に華やかになってきましたね。

ここまでのソースコードは [https://github.com/Onebase-Fujitsu/todo-app-client/tree/step3](https://github.com/Onebase-Fujitsu/todo-app-client/tree/step3) に置いてあります。

---

一日目はクライアントアプリの環境構築と最初の画面表示までいきました。
環境構築はアプリ構築になれている方がやらないとかなり難しい部分だったりします。
というのもアプリの初期構築はそんなに回数こなすものでもないためです。

特にReactは最初の環境構築がcreate-react-appで簡略化されたとはいえ難しいです。
まだReactのフレームワークも触りの部分しか扱っていません。
ここまで、躓くことが無いように頑張ってください。

---

2日目に続きます

{{< button relref="/docs/practice/day2" >}}2日目{{< /button >}}
