---
title: "E2Eテスト"
weight: 5
bookToc: true
---

# E2Eテスト

## サンプルアプリケーション

もし前のテストダブルの節をすでに読んでいて、リポジトリをclone済みの場合は以下のコマンドを実行してください。

```shell
git checkout e2etest
```

もし、この節から読み始めている場合は、以下のコマンドを実行してください。

```shell
git clone -b e2etest https://github.com/Onebase-Fujitsu/simple-todo-app.git
```

このアプリケーションはシンプルなタスク管理アプリケーションになります。
[アプリケーションの詳細な説明はこちら]({{< ref "/docs/softwareTest/testDouble#サンプルアプリケーション" >}}) で解説してありますので、ご一読ください。

e2eブランチではmainブランチと違い、ルートのディレクトリにe2eというブランチが作成されています。

まず以下のコマンドを実行してみてください。

e2eテストの起動(Mac/Linuxの場合)
```shell
cd e2e
npm install
npm run test
```

e2eテストの起動(Windowsの場合)
```shell
chdir e2e
npm install
npm run test
```

`npm run start`コマンドを実行するとChromeのウィンドウが一瞬開いてすぐ閉じると思います。
もしChromeをインストールされていない方は [こちらからChromeをインストール](https://www.google.com/intl/ja_jp/chrome/) して試してみてください。

![testcafe](testcafe.jpg)

この画面が出たらこのセクションを始める準備は大丈夫です。