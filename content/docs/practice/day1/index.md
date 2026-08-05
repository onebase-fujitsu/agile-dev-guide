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

{{% hint info %}}

**開発言語変更の例**

Javascriptとそのスーパーセットである、Typescriptはプロジェクト内で共存が可能で、
順次置き換えていくと言った戦略が取れるでしょう。
また、JavaとKotlinも順次置き換える戦略がとることができ、
現に筆者のプロジェクトでは当初JavascriptとJavaを採用していましたが、
開発に着手して１ヶ月半ほどでTypescriptとKotlinに変更しました。
しかし、これはまだ着手してまもなく規模もそこまで大きくなかったため踏み切れたものです。

ソフトウェアの開発規模が大きくなるにつれ次第に変更は難しくなります。
最初の開発言語は慎重にやるべきです。
{{% /hint %}}

## プロジェクトの構成

今回は簡単なタスク管理アプリを作ってみます。
これから作るのはフロントエンドはReactで開発言語はTypescript、バックエンドはSpring Bootで開発言語はKotlinで、
フロントエンドとバックエンドがHTTPでJSONをやり取りして、画面を描画する極々シンプルなSPAアプリケーションです。

![アーキテクチャ](arch.jpg)


ディレクトリ構成は以下の形で進めていきます。
```
todo-app
├── e2e                 // e2eテスト用のディレクトリ
├── todo-app-client     // Client Appのディレクトリ
└── todo-app-server     // Backend Appのディレクトリ
```

まずは、任意の場所にtodo-appディレクトリを作成しましょう。


## クライアントの環境構築

{{% hint warning %}}

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

{{% /hint %}}

### reactアプリの作成

{{% hint info %}}

**Node.jsのバージョン**

このハンズオンで使うツールにはNode.jsのバージョン要件があります。
特に4日目に導入するルーティングライブラリが`Node.js 22.22.0`以上を要求するため、
最初から**Node.js 22.22.0以上**(推奨は最新のLTS)を用意しておいてください。

`node -v`で確認できます。

{{% /hint %}}

```shell
npm create vite@latest todo-app-client -- --template react-ts
```

今回はtypescriptのtemplateから作成を始めます。
[vite](https://vite.dev/guide/)を使ってクライアントの雛形を作ります。

コマンドを実行するといくつか質問されるので、以下のように答えてください。

```console
onebase@Onebase-Maguro todo-app % npm create vite@latest todo-app-client -- --template react-ts

> npx
> create-vite todo-app-client --template react-ts

To create in one go, run: create-vite <DIRECTORY> --no-interactive --template <TEMPLATE>

│
◆  Which linter to use?
│  ○ Oxlint
│  ● ESLint
│  ↑/↓ to navigate • Enter: confirm
└
│
◆  Install with npm and start now?
│  ○ Yes / ● No
└
│
◇  Scaffolding project in /Users/onebase/IdeaProject/todo-app/todo-app-client...
│
└  Done. Now run:

  cd todo-app-client
  npm install
  npm run dev
```

`Which linter to use?`では**初期選択がOxlintになっている**ので、
`↓`キーでESLintに切り替えてからEnterを押してください。  
OxlintはRust製で非常に高速なlinterですが、本ガイドではプラグインのエコシステムが成熟している**ESLint**を選択します。

`Install with npm and start now?`は**初期選択がYes**です。
Yesのままだと依存関係のインストールと開発サーバの起動まで一気に進みます。
ここでは手順を1つずつ確認するため、`→`キーで**No**を選んでください。

{{% hint info %}}

**対話をスキップしたい場合**

`--eslint`フラグを付けると`Which linter to use?`の質問を省略できます。

```shell
npm create vite@latest todo-app-client -- --template react-ts --eslint
```

{{% /hint %}}

表示された指示に従って、以下を実行します。

```shell
cd todo-app-client
npm install
npm run dev
```

※`npm run dev`を実行し、起動できることを確認したらCtrl + Cで終了してください。

このTemplateにはサンプルページの実装が含まれていますので、不要なものを削除します。

- `src/App.css`を削除
- `src/assets`ディレクトリごと削除
- `public/icons.svg`を削除(`public/favicon.svg`は`index.html`から参照されているので残します)
- `src/index.css`は**削除せず中身を空に**する(後でスタイルを書きます)
- `src/App.tsx`を以下の内容に書き換える

```typescript jsx
// App.tsx
const App = () => <div className="App" />

export default App
```

結果、srcディレクトリは以下の形になります。

```
src
├── App.tsx
├── index.css
└── main.tsx
```

{{% hint info %}}

**vite-env.d.tsは生成されなくなりました**

以前のテンプレートには`src/vite-env.d.ts`というファイルがあり、
`import.meta.env`などVite固有の型を読み込む役割を持っていました。

現在は`tsconfig.app.json`の`"types": ["vite/client"]`がその役割を担っているため、このファイルは生成されません。
古い記事に出てきても探さないでください。

{{% /hint %}}

### eslintの設定

一番最初にやるべきはlintの設定です。lintとはコードが規約に準じているかを確認してくれるライブラリです。

先ほど雛形を作るときに`Which linter to use?`でESLintを選んだため、
必要なパッケージのインストールと設定ファイルの生成はすでに終わっています。  
とはいえ、自分で書いていない設定ファイルこそ中身を読んで理解しておきましょう。

`todo-app-client`直下に生成された`eslint.config.js`はこのようになっています。

```javascript
// eslint.config.js
import js from '@eslint/js'
import globals from 'globals'
import reactHooks from 'eslint-plugin-react-hooks'
import reactRefresh from 'eslint-plugin-react-refresh'
import tseslint from 'typescript-eslint'
import { defineConfig, globalIgnores } from 'eslint/config'

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      js.configs.recommended,
      tseslint.configs.recommended,
      reactHooks.configs.flat.recommended,
      reactRefresh.configs.vite,
    ],
    languageOptions: {
      globals: globals.browser,
    },
  },
])
```

これは [Flat Config](https://eslint.org/docs/latest/use/configure/configuration-files) と呼ばれる形式です。
それぞれの意味は以下の通りです。

- `globalIgnores(['dist'])` : ビルド成果物である`dist`をlintの対象外にします
- `files: ['**/*.{ts,tsx}']` : 以降の設定を`.ts`と`.tsx`にだけ適用します
- `js.configs.recommended` : ESLint本体の推奨ルール
- `tseslint.configs.recommended` : TypeScript向けの推奨ルール
- `reactHooks.configs.flat.recommended` : Hooksのルールを強制します
- `reactRefresh.configs.vite` : Viteの Fast Refresh が壊れる書き方を検出します
- `languageOptions.globals` : ブラウザで動くコードとして`window`などのグローバル変数を認識させます

package.jsonのscriptにも、すでにlintコマンドが登録されています。

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "lint": "eslint .",
    "preview": "vite preview"
  }
}
```

設定が効いているか確認してみましょう。`src/App.tsx`をわざと規約違反の状態にしてみます。

```typescript jsx
// App.tsx
const App = () => {
  const message: any = 'Todo App'      // 追加(わざとanyを使う)
  return <div className="App">{message}</div>
}

export default App
```

この状態で`npm run lint`を実行するとエラーが出力されるはずです。

```bash
onebase@Onebase-Maguro todo-app-client % npm run lint

> todo-app-client@0.0.0 lint
> eslint .


/Users/onebase/IdeaProject/todo-app/todo-app-client/src/App.tsx
  2:18  error  Unexpected any. Specify a different type  @typescript-eslint/no-explicit-any

✖ 1 problem (1 error, 0 warnings)
```

注目してほしいのは、**このコードはTypeScriptのコンパイル自体は通る**という点です。
`npm run build`を実行しても何も言われません。それでもlintは止めてくれます。
コンパイラが許すが書くべきでないコードを検出してくれる、というのがlintを入れる理由です。

確認できたら`App.tsx`を元に戻しておいてください。

```typescript jsx
// App.tsx
const App = () => <div className="App" />

export default App
```

{{% hint warning %}}

**TypeScriptのバージョンは上げないでください**

`typescript-eslint`が対応するTypeScriptのバージョンには上限があります。
テンプレートが指定している`typescript`のバージョンを勝手に上げると、
lint実行時にサポート外のバージョンであるという警告が出るようになります。

{{% /hint %}}


### prettierの設定

指摘するだけですと不便ですので、自動で修正してくれるようにprettierを導入します。   
まず、eslintからprettierの競合設定を外す拡張を導入します。

```bash
npm install --save-dev eslint-config-prettier
```

`eslint.config.js`にeslint-config-prettierの設定を追加します。  
**`extends`配列の一番最後**に置くのがポイントです。
Flat Configでは後ろに書いた設定ほど優先されるため、最後に置くことでprettierと競合するルールをすべて打ち消せます。

```javascript
// eslint.config.js
import js from '@eslint/js'
import globals from 'globals'
import reactHooks from 'eslint-plugin-react-hooks'
import reactRefresh from 'eslint-plugin-react-refresh'
import tseslint from 'typescript-eslint'
import { defineConfig, globalIgnores } from 'eslint/config'
import eslintConfigPrettier from 'eslint-config-prettier/flat'   // 追加

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      js.configs.recommended,
      tseslint.configs.recommended,
      reactHooks.configs.flat.recommended,
      reactRefresh.configs.vite,
      eslintConfigPrettier,                                      // 追加
    ],
    languageOptions: {
      globals: globals.browser,
    },
  },
])
```

次にprettierを導入します。

```bash
npm install --save-dev prettier
```

`todo-app-client`ディレクトリ配下に`.prettierrc`ファイルを作成します。

```
// .prettierrc
{
    "singleQuote": true,
    "tabWidth": 2,
    "semi": false,
    "bracketSpacing": false,
    "bracketSameLine": true
}
```

package.jsonのscriptに修正用のコマンドも入れてしまいましょう。

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "lint": "eslint .",
    "preview": "vite preview",
    "format": "prettier --write 'src/**/*.{js,jsx,ts,tsx}'",  // 追加
    "lint:fix": "eslint --fix .",                             // 追加
    "fix": "npm run format && npm run lint:fix"               // 追加
  }
}
```

これで設定は完了です。`npm run fix`を実行してみましょう。

```bash
onebase@Onebase-Tsurumi todo-app-client % npm run fix

> todo-app-client@0.0.0 fix
> npm run format && npm run lint:fix


> todo-app-client@0.0.0 format
> prettier --write 'src/**/*.{js,jsx,ts,tsx}'

src/App.tsx 25ms (unchanged)
src/main.tsx 4ms

> todo-app-client@0.0.0 lint:fix
> eslint --fix .

```

`src/main.tsx`を開いてみてください。`import { StrictMode } from 'react'`が
`import {StrictMode} from 'react'`に書き換わっているはずです。
`.prettierrc`で`bracketSpacing`を`false`にしたためです。

これらの設定により、書き方の揺れがprettierにより自動で修正されるようになります。 

ここまでのソースコードは [https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step1](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step1) においてあります。

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

{{% hint info %}}

**styled-componentsの利点**

styled-componentsで定義するスタイルはReactのコンポーネントそのものです。このため、styled-componentsを利用することで、componentとstyleのマッピングが無くなります。

さらに、ローカルスコープで利用できるため、複数のコンポーネントで同一の名前が使用でき、長い命名に悩まされる必要がなくなります。

また、styled-componentsを使用すると、CSSコードを記述してコンポーネントのスタイルを設定できるため、他のCSSフレームワークに比べて学習コストが低いことも利点の一つです。

{{% /hint %}}

styled-componentsの導入方法は[こちら](https://styled-components.com/)に書いてあるので、これに習って導入してみましょう。

styled-componentsの導入は非常に簡単で以下のコマンドを実行するだけです。

```shell
npm install --save styled-components
```

{{% hint info %}}

**@types/styled-componentsは不要です**

styled-componentsはv6からパッケージ自身が型定義を同梱しているため、`@types/styled-components`をインストールする必要はありません。
古い記事では一緒にインストールするよう書かれていることがありますが、入れると型が競合します。

{{% /hint %}}

これでstyled-components導入ができました。
正しく導入できているか確認してみましょう。

App.tsxを以下のように変更してコンパイルしてみましょう。

```typescript jsx
// App.tsx
import styled from 'styled-components'

const Div = styled.div`
  color: red;
`

const App = () => <Div>test</Div>

export default App
```

```shell
npm run dev
```

![styled-componentsの動作確認](tailwind.jpg)

ブラウザで赤い文字でtestと表示されているのが確認できたら正しく導入できていますので、確認できたらApp.tsxをもとに戻しておいてください。

ここまでのソースは [https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step2](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step2) に置いてあります。

### テスト環境の整備

次にクライアントのテスト環境を整備していきます。  
ReactのTestライブラリとして著名なものにenzymeとReact Testing Libraryがあります。  
どちらを使ってもいいのですが、enzymeはreact16までしか対応しておらず現在はメンテナンスされていないため、  
今回はReact Testing Libraryを採用することにします。  

テストランナーには**Vitest**を使います。  
Jestも広く使われていますが、今回はビルドツールにViteを使っているため、  
同じ変換の仕組みをそのまま流用できるVitestのほうが設定が圧倒的に簡単です。  
`ts-jest`やBabelの設定が一切要らず、`describe`/`it`/`expect`といったAPIはJestとほぼ同じです。

必要なパッケージをインストールします
- vitest: Viteの仕組みをそのまま使えるテストランナー
- jsdom: Node.js上でブラウザのDOMを再現するために必要
- @testing-library/react: React で Testing Library を使用するために必要
- @testing-library/jest-dom: `toBeInTheDocument`などのDOM要素のカスタムマッチャーを提供

```bash
npm install --save-dev vitest jsdom
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

Vitestの設定は`vite.config.ts`に書きます。  
設定ファイルが分かれていないので、プラグインやパス解決の設定を開発サーバとテストで二重管理せずに済みます。

`import`元が`vite`から`vitest/config`に変わっている点に注意してください。
`test`という項目はVitestが追加しているものなので、`vitest/config`の`defineConfig`を使わないと型エラーになります。

```typescript
// vite.config.ts
import { defineConfig } from 'vitest/config'   // 更新
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  test: {                                      // 追加
    globals: true,                             // 追加
    environment: 'jsdom',                      // 追加
    setupFiles: ['./test/setup.ts'],           // 追加
  },                                           // 追加
})
```

- `globals: true` : `describe`や`it`、`expect`をimportなしで使えるようにします
- `environment: 'jsdom'` : テストをブラウザに近い環境(DOMがある状態)で動かします
- `setupFiles` : すべてのテストファイルの前に読み込まれるファイルを指定します

Vitestはデフォルトで`**/*.{test,spec}.{ts,tsx}`にマッチするファイルをテストとして実行します。  
どこに置いてもよいのですが、今回はsrcディレクトリと並列に`test`ディレクトリを作成する形式でいきます。

`todo-app-client`直下に`test`ディレクトリを作り、その中に`setup.ts`を作成して、
Testing Library のセットアップを行います。

```typescript
// test/setup.ts
import '@testing-library/jest-dom/vitest'
```

この1行で`toBeInTheDocument()`のようなDOM向けのマッチャーが`expect`に追加されます。

最後に`tsconfig.app.json`を更新します。

```json
{
  "compilerOptions": {
    // 中略
    "types": ["vite/client", "vitest/globals"]   // 更新
  },
  "include": ["src", "test"]                     // 更新
}
```

- `types`に`vitest/globals`を追加することで、importせずに使う`describe`や`expect`の型が認識されます。
  元々あった`vite/client`を消してしまわないよう注意してください
- `include`に`test`を追加することで、テストコードも型チェックの対象になります。
  同時に`test/setup.ts`も対象に入るため、先ほど追加したマッチャーの型も効くようになります

package.json にテストコマンドを追加します。

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "lint": "eslint .",
    "preview": "vite preview",
    "format": "prettier --write 'src/**/*.{js,jsx,ts,tsx}'",
    "lint:fix": "eslint --fix .",
    "fix": "npm run format && npm run lint:fix",
    "test": "vitest",                    // 追加
    "test:run": "vitest run"             // 追加
  }
}
```

`npm run test`を実行するとテストランナーが立ち上がり、ファイルの変更を監視しはじめます。
ファイルの変更があるたびに、関係するテストだけを実行してくれるようになります。
CIなどで1回だけ実行して結果を得たい場合は`npm run test:run`(`vitest run`)を使います。

ただし、まだテストファイルが1つもないため、今実行しても以下のように終了します。
次のセクションで最初のテストを書いてから実行しましょう。

```
No test files found, exiting with code 1

include: **/*.{test,spec}.?(c|m)[jt]s?(x)
exclude:  **/node_modules/**, **/.git/**
```

{{% hint info %}}

**Vitestは型チェックをしません**

VitestはTypeScriptを高速に変換して実行しますが、そのとき**型チェックは行いません**。
型の誤りを検出したいときは`npm run build`(`tsc -b`)を実行してください。

テストが通っていても`npm run build`が失敗することはあります。
コミット前には`npm run test:run`と`npm run build`、`npm run lint`の3つを通す習慣をつけましょう。

{{% /hint %}}

## ヘッダーの作成

### テストの作成

`todo-app-client`直下にtestディレクトリを作成し、その配下にcomponentsディレクトリを作成して、その配下にHeader.test.tsxを作りましょう。

```
├── src
│    ├── App.tsx
│    ├── index.css
│    └── main.tsx
└── test
     ├── components
     │    └── Header.test.tsx
     └── setup.ts
```

```typescript jsx
// Header.test.tsx
import {cleanup, render, screen} from '@testing-library/react'

describe('Header', () => {
  afterEach(() => {
    cleanup()
  })

  it('ヘッダーの初期表示', () => {
    render(<Header />)
    expect(screen.getByText('Todo App')).toBeInTheDocument()
  })
})
```

最初のテストはこのようにしてみました。`Todo App`という文字が表示されていることを確認しています。

この時点ではHeaderコンポーネントは作成されていないので、当然失敗します。
`npm run test`を実行するとこのような表示になっているはずです。

```
> todo-app-client@0.0.0 test
> vitest


 RUN  v4.1.10 /Users/onebase/IdeaProject/todo-app/todo-app-client

 ❯ test/components/Header.test.tsx (1 test | 1 failed) 2ms
     × ヘッダーの初期表示 1ms

⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯

 FAIL  test/components/Header.test.tsx > Header > ヘッダーの初期表示
ReferenceError: Header is not defined
 ❯ test/components/Header.test.tsx:10:13
      8|
      9|   it('ヘッダーの初期表示', () => {
     10|     render(<Header />)
       |             ^
     11|     expect(screen.getByText('Todo App')).toBeInTheDocument()
     12|   })

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed (1)
      Tests  1 failed (1)
   Start at  15:32:48
   Duration  384ms (transform 12ms, setup 43ms, import 21ms, tests 2ms, environment 257ms)
```

Vitestは型チェックをしないため、「`Header`という名前が見つからない」という型エラーではなく、
テストを実行した結果の`ReferenceError`として失敗します。


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
// Header.test.tsx
import {cleanup, render, screen} from '@testing-library/react'
import Header from '../../src/components/Header'

describe('Header', () => {
  afterEach(() => {
    cleanup()
  })

  it('ヘッダーの初期表示', () => {
    render(<Header />)
    expect(screen.getByText('Todo App')).toBeInTheDocument()
  })
})
```

このファイルを保存すると、監視中のテストが再実行され、無事通っていることがわかると思います。

```
 RUN  v4.1.10 /Users/onebase/IdeaProject/todo-app/todo-app-client


 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  15:33:05
   Duration  378ms (transform 15ms, setup 43ms, import 20ms, tests 11ms, environment 244ms)
```

最初のテストが通りました！おめでとうございます。

{{% hint info %}}

**どのテストが通ったのか一覧で見たいとき**

Vitestの標準のレポーターは、すべて成功したときは件数だけを表示します。
1件ずつ確認したいときは`--reporter=verbose`を付けて実行してください。

```bash
npx vitest run --reporter=verbose
```

```
 ✓ test/components/Header.test.tsx > Header > ヘッダーの初期表示 29ms

 Test Files  1 passed (1)
      Tests  1 passed (1)
```

{{% /hint %}}

## 画面の表示

Headerは作りましたがまだReactアプリに組み込んでいないため、まだブラウザ上ではなにも表示されていません。
次に画面に表示するようにしてみましょう。

コンポーネントのときと同じようにtest配下にpagesディレクトリを作成し、`Home.test.tsx`を作成します。
同様にsrc/pages配下に`Home.tsx`を作成しましょう。

```
├── src
│    ├── App.tsx
│    ├── components
│    │    └── Header.tsx
│    ├── index.css
│    ├── main.tsx
│    └── pages
│         └── Home.tsx
└── test
     ├── components
     │    └── Header.test.tsx
     ├── pages
     │    └── Home.test.tsx
     └── setup.ts
```

```typescript jsx
// Home.test.tsx
import {cleanup, render, screen} from '@testing-library/react'
import Home from '../../src/pages/Home'

describe('Home画面', () => {
  afterEach(() => {
    cleanup()
  })

  it('ホーム画面の初期表示', () => {
    render(<Home />)
    expect(screen.getByText('Todo App')).toBeInTheDocument()
  })
})
```

```typescript jsx
// Home.tsx
import Header from '../components/Header'

const Home = () => (
  <div>
    <Header />
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

`npm run dev`を実行して [http://localhost:5173](http://localhost:5173) にアクセスしてみましょう。

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

次にブラウザのデフォルトスタイルを打ち消します。

雛形作成のときに中身を空にした`src/index.css`に、以下を記述します。

```css
/* index.css */
body {
  margin: 0;
}
```

`src/index.css`はテンプレートの`main.tsx`がすでに読み込んでいるため、`main.tsx`の変更は不要です。

```typescript jsx
// main.tsx
import {StrictMode} from 'react'
import {createRoot} from 'react-dom/client'
import './index.css'
import App from './App.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

以上でスタイルの適用は完了です。

![ヘッダーにデザイン適用](home_apply_design.jpg)

ヘッダーに背景色をつけて、Headerの文字を白に、そして大きくしてみました。
一気に華やかになってきましたね。

ここまでのソースコードは [https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step3](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step3) に置いてあります。

{{% hint warning %}}

**styled-components使用時の注意点**

実際にstyled-componentsを使用してお気づきかもしれませんが、styled-componentsは非常に自由度の高いCSSフレームワークです。

そのため、チームメンバーがそれぞれ自由に開発を進めると、可読性が下がりバグの原因に繋がります。
例えばデザインのみを定義したcomponentと、何か機能を持ったcomponentとを一見して見分けることができません。
また、定義したcomponentの全体像がわかりにくくなります。

そのためチームで開発する際には、ディレクトリ構成やファイル構造、命名規則などのルールを定めて使用しましょう。

{{% /hint %}}

---

一日目はクライアントアプリの環境構築と最初の画面表示までいきました。
環境構築はアプリ構築になれている方がやらないとかなり難しい部分だったりします。
というのもアプリの初期構築はそんなに回数こなすものでもないためです。

特にReactは最初の雛形作成が`npm create vite`でかなり簡略化されたとはいえ、
lint、フォーマッタ、テストランナーをどう組み合わせるかは自分で決める必要があり、難しい部分です。
まだReactのフレームワークも触りの部分しか扱っていません。
ここまで、躓くことが無いように頑張ってください。

---

2日目に続きます。

{{% button relref="/docs/practice/day2" %}}2日目{{% /button %}}
