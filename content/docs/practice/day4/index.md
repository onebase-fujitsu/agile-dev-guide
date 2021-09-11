---
title: "4日目"
weight: 4
bookToc: true
---

# 4日目

3日まででReact+Reduxで作ったフロントエンドとSpring Bootで作ったバックエンドを連携させることができました。
しかし、タスクを表示することができましたが、タスクを追加する機能がまだありません。
4日目からはどんどん機能を追加してみましょう。

## タスクの追加画面を作る

いまClientのアプリケーションにはまだルーターの機能がありません。
例えば、 [http://localhost:3000/](http://localhost:3000/) にアクセスしてもHome画面が表示されていますし、
[http://localhost:3000/hogehoge](http://localhost:3000/hogehoge) にアクセスしてもHome画面が表示されます。

これはちょっとイケてないので、ルーターの機能を実装してみます。
ここでは [http://localhost:3000/](http://localhost:3000/) にアクセスするとHome画面が、
そして [http://localhost:3000/newTodo](http://localhost:3000/newTodo) にアクセスするとタスク管理画面が開くようにしてみましょう。

### ルーターの導入

ルーターの実装には [react-router-dom](https://reactrouter.com/) が必要なので、これを導入します。
これはルーティング機能もDOMとして宣言してしまうライブラリです。
これだけどあまりピンとこないと思いますので、どういう実装になるのかを実際に見てもらった方が早いと思います。

まずreact-router-domを導入します。

```shell
npm install react-router-dom @types/react-router-dom
```

そして、App.tsxを以下のように編集してみましょう

```typescript jsx
import {BrowserRouter, Route, Switch} from "react-router-dom";
import Home from './pages/Home'

function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <Switch>
          <Route path="/" exact render={() => <Home/>} />
        </Switch>
      </BrowserRouter>
    </div>
  )
}

export default App
```

この状態で [http://localhost:3000/](http://localhost:3000/) にアクセスするとHome画面が表示されます。ここはこれまでの動作と変わりません。
そこで [http://localhost:3000/hoge](http://localhost:3000/hoge) にアクセスすると、先程と代わって真っ白な画面になると思います。

つまりこれは'/'というパスだったらホームコンポーネントを表示するよという設定なわけです。

