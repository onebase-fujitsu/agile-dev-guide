---
title: "6日目"
weight: 6
bookToc: true
---

# 6日目

クライアントとサーバを連携させてタスクを作成することができるようになったので、シナリオテストを作成していきましょう。

## e2eテストの環境構築

まずe2eテスト用の環境を作成していきましょう。

```shell
mkdir e2e
cd e2e
mkdir test
touch test/test.js 
npm init
```

`npm init`コマンドを実行すると、設定ダイアログが表示されるので、いったんすべて何も設定せずEnterを押下してpackage.jsonを作成。
その後、e2eテストライブラリのtestcafeを導入します。

```shell
npm install --save-dev testcafe
```

インストールが完了したら、package.jsonを以下のように編集します。

```json
{
  "name": "todo-app",
  "version": "1.0.0",
  "main": "test/test.js",
  "directories": {
    "test": "test"
  },
  "scripts": {
    "test": "./node_modules/.bin/testcafe 'chrome' ./test"
  },
  "author": "",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "testcafe": "^3.7.1"
  }
}
```

編集が完了したらe2eディレクトリで以下のコマンドを実行してみましょう。
Chromeが起動し画像のような画面が一瞬起動して閉じると環境の準備は完了です。

```shell
npm run test
```

![testcafe](testcafe.jpg)


ここまでのリポジトリは
[https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step12](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step12)
においてあります。

## e2eテストの作成

では早速e2eテストを作成していきましょう。まず、トップページが表示されることを確認しましょう。
test.jsを以下のように変更します。

```javascript
// test.js
import { Selector } from 'testcafe';

fixture `test target`
    .page `http://localhost:5173`;

test('タスク一覧画面を見ることができる', async t => {
    await t
        .expect(Selector('body').withText('Todo App').exists).ok()
        .expect(Selector('a').withText("Home").exists).ok()
        .expect(Selector('a').withText("New Task").exists).ok()
})
```

そして**clientアプリとserverアプリを起動した状態で**`npm run test`コマンドを実行してみてください。
問題なければ、テストが成功するはずです。

```
onebase@Onebase-Maguro e2e % npm run test

> todo-app@1.0.0 test
> ./node_modules/.bin/testcafe 'chrome' ./test

 Running tests in:
 - Chrome 93.0.4577.82 / macOS 10.15.7

 test target
 ✓ タスク一覧画面を見ることができる


 1 passed (1s)
```

テストが通ったらさらにテストを追加して、タスクが追加できることを確認しましょう。

```javascript
// test.ts
import { Selector } from 'testcafe';

fixture `test target`
    .page `http://localhost:5173`;

test('タスク一覧画面を見ることができる', async t => {
    await t
        .expect(Selector('body').withText('Todo App').exists).ok()
        .expect(Selector('a').withText("Home").exists).ok()
        .expect(Selector('a').withText("New Task").exists).ok()
})

test('タスクを作成することができる', async t => {
    await t
        .click(Selector('a').withText("New Task"))
        .expect(Selector('input').exists).ok()
        .expect(Selector('button').withText("Send").exists).ok()
        .typeText(Selector('input'), 'e2e test title', {replace: true})
        .click(Selector('button').withText("Send"))
        .click(Selector('a').withText("Home"))
        .expect(Selector('ul').child().nth(-1).textContent).contains('e2e test title')
})
```

これも実行すると問題なく通るはずです。

ここまでのリポジトリは
[https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step13](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step13)
にあります。

## さらなるアプリのスケールに向けて

ここまで、簡単なSPAのアプリケーションを1からテスト駆動開発で作成し、バックエンドのAPIと連携させ、
一連のプロセスをE2Eテストするというところまで来ました。

しかし、Componentの分割粒度も曖昧なままです。
複数人で開発をすすめるとどうしても開発者によってComponentの分割粒度にばらつきがでます。
意図せず巨大なComponentができてしまうかもしれません。
そこで取り入れたいのが [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/) の考え方です。

アプリ開発はとにかく自由度が大きいです。アジャイル開発となれば開発に関する裁量は開発者に委ねられるため、
無数にある開発手法の中から最適なものを選択し、アーキテクチャを決定して、実装していかなければなりません。

そして、自由度が高くなればなるほどに、個人間での開発スタイルやスキルの"ブレ"が問題になってきます。
**チーム開発において最も大事なのは規約です。**
このハンズオンでも一番最初にやったのはeslintの設定でした。規約はとにかく大事です。

コンポーネントの分割粒度はこうする、バックエンドのAPIはこうするとチーム内でかならず合意形成をとり、
lintのように自動でチェックできるものはlintに規約を落とし込んでコードの品質を保つようにしてください。

もし、コードの品質を保つことできないメンバーがいるようなら、チーム内で勉強会などを開くと良いです。
そして、そもそもとしてコードの品質を保つことができないようなメンバーをその時点で参画させるべきではありません。
事前に教育を受けてもらうなどし、一定のスキル水準に達したところでプロジェクトに参画してもらうべきでしょう。

まだバックエンドの変更API`PATCH /todos/{id}`も実装できていませんし、まだまだやることは山のようにあります。
**アジャイルのアプリ開発に終わりはありません。**
さらなるユーザ価値の提供に向けて各自取り組んでいってください。

---

まとめに続きます

{{< button relref="/docs/inClosing" >}}おわりに{{< /button >}}
