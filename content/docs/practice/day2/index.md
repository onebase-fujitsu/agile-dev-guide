---
title: "2日目"
weight: 2
bookToc: true
---

# 2日目

2日目はClientがServerに対してHTTPリクエストを実行するところ、そして、Serverの初期設定をして簡単なAPIを作って見るところをやってみましょう。
ここからだいぶ難しくなると思いますが、頑張っていきましょう。

## 非同期通信のテストと実装

### 非同期通信のテスト

まず、トップ画面を開いたらタスクの一覧を取得して、取得できたタスク一覧を表示するという機能を作ってみましょう。
Webブラウザには [fetchAPI](https://developer.mozilla.org/ja/docs/Web/API/Fetch_API) という機能があります。
ReactからfetchAPIを実行すると、指定したURLからリソースを取得することができます。

しかし、今回はこのfetchAPIはしようせず、 [axios](https://axios-http.com/) を導入します。
axiosはnode.jsのHTTP Clientです。axiosの類似のライブラリとしては [isomorphic-fetch](https://www.npmjs.com/package/isomorphic-fetch) などもあります。

なぜブラウザ標準のAPIを使用しないかというと以下の理由からです。

- ブラウザによってfetchの実装に差異がある
- ブラウザの実装をMockしてテストする難しさ

HTTP Clientをモックしてしまえば、どんなレスポンスもスタブとしてセットできるようになるので、開発がしやすくなります。

まずはaxiosとモックライブラリであるaxios-mock-adapterを導入しましょう。

```shell
npm install axios @types/axios
npm install --save-dev axios-mock-adapter
```

さて、テストの実装をしていく前に今回の機能を実現するAPIを軽く設計しないといけません。
ひとまず今回は`/todos`に対してGETすると以下のフォーマットのJSON文書が取得できるとします。

```json
[
  {
    "id": number,
    "title": string,
    "completed": boolean
  }
]
```

ルートが配列になっているJSONですね。エンベロープにくるむ(つまり最初にtodosのようなキーを作る)べきか、くるまない方がいいのかは議論が分かれるところですが、
筆者は後者の方ががいいと考えています。`GET /todos`というエンドポイントからTodoを一覧で取得できることが明確だからです。

さて、ではテストを書いていきましょう。
`test`配下にfeaturesというディレクトリを作成し、`TodoApi.test.ts`というファイルを作成し、最初のテストを実装していきます。

```typescript
// TodoApi.test.ts
import MockAdapter from 'axios-mock-adapter'
import axios from 'axios'
import {getTodos} from '../../features/TodoApi'

describe('TodoApi', () => {
  let mock: MockAdapter

  beforeEach(() => {
    mock = new MockAdapter(axios)
  })

  afterEach(() => {
    mock.reset()
  })

  it('get /todos', async () => {
    mock.onGet('/todos').reply(200, [
      {
        id: 1,
        title: 'title',
        completed: false,
      },
    ])

    const response = await getTodos()

    expect(mock.history.get[0].url).toEqual('/todos')
    expect(response[0].id).toEqual(1)
    expect(response[0].title).toEqual('title')
    expect(response[0].completed).toEqual(false)
  })
})
```

最初のテストはこのようにしました。axios-mock-adapterでHTTP通信をモックしており、
getTodo()を実行したときに、ちゃんと`/todo`に対してGetのリクエストをしていること。
戻り値のJSONのパースがうまくいっていることを確認しています。

### 非同期通信の実装

テストが実装できたら、これを実装してみましょう。
src配下にfeaturesというディレクトリを作成し、TodoApi.tsを作成します。

```typescript
// TodoApi.ts
import axios from 'axios'

export interface Todo {
  id: number
  title: string
  completed: boolean
}

export const getTodos = async () => {
  const response = await axios.get<Todo[]>('/todos')
  return response.data
}
```

実装はこのようになりました。ひとまずこれでAPIのテストは通るはずです。
テストが通ったのを確認したら次のステップに行きましょう。

## タスク一覧表示コンポーネントのテストと実装

これからタスク一覧のコンポーネントを作成します。

### タスク一覧表示コンポーネントのテスト

`test/components`配下にTodoList.test.tsxを作成しましょう。

```typescript jsx
//TodoList.test.tsx
import {render, screen} from '@testing-library/react'
import TodoList from '../../src/components/TodoList'
import MockAdapter from "axios-mock-adapter";
import axios from "axios";

describe('TodoList.tsx Component', () => {
  let mock: MockAdapter

  beforeEach(() => {
    mock = new MockAdapter(axios)
  })

  afterEach(() => {
    mock.reset()
  })

  it('getTodosの戻り値が空ならリストも空', () => {
    mock.onGet('/todos').reply(200, [])

    render(<TodoList />)

    expect(screen.getByRole('list').hasChildNodes()).toEqual(false)
  })
})

```

最初のテストはこのようにしてみました。これを実行してみましょう。
TodoListはまだ実装されていませんので、当然失敗するはずです。

```
 FAIL  test/components/TodoList.test.tsx
  ● Test suite failed to run

    test/components/TodoList.test.tsx:3:22 - error TS2307: Cannot find module '../../src/components/TodoList' or its corresponding type declarations.

    3 import TodoList from '../../src/components/TodoList'
                           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Test Suites: 1 failed, 3 passed, 4 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        0.695 s, estimated 1 s
Ran all test suites.
```

### タスク一覧表示コンポーネントの実装

ではコンポーネントを実装していきましょう。src/components配下にTodoList.tsxを作成します。

```typescript jsx
// TodoList.tsx
import {useEffect, useState} from 'react'
import {getTodos, Todo} from '../features/TodoApi'

const TodoList = () => {
  const [todos, setTodos] = useState<Todo[]>([])

  useEffect(() => {
    const fetchData = async () => {
      const response = await getTodos()
      setTodos(response)
    }
    fetchData()
  }, [])

  return (
          <ul data-testid="TodoList">
            {todos.map((todo) => (
                    <li key={todo.id}>{todo.title}</li>
            ))}
          </ul>
  )
}

export default TodoList
```

`useState()`は関数コンポーネントでstateを管理するためのReactが提供している関数です。
さらに、`useEffect()`を利用し、画面描画時にtodosの中身にgetTodosの戻り値を詰めています。

また`useEffect()`で非同期処理を呼び出す場合は、直接関数を実行するとメモリーリークの原因となるため注意が必要です。
今回は`useEffect()`内で非同期関数を定義し、それを実行することで回避しています。
`useState()`や`useEffect()`について詳しく知りたい方は[こちら](https://ja.react.dev/reference/react/hooks)を参照してください。

### リスト表示のテスト

リストが返却された場合のテストも追加してみましょう。

```typescript jsx
// TodoList.test.tsx
import {render, screen} from '@testing-library/react'
import TodoList from '../../src/components/TodoList'
import MockAdapter from 'axios-mock-adapter'
import axios from 'axios'
import {act} from "react";

describe('TodoList.tsx Component', () => {
  let mock: MockAdapter

  beforeEach(() => {
    mock = new MockAdapter(axios)
  })

  afterEach(() => {
    mock.reset()
  })

  it('getTodosの戻り値が空ならリストも空', async () => {
    mock.onGet('/todos').reply(200, [])

    await act(() => {
      render(<TodoList />)
    })

    expect(screen.getByRole('list').hasChildNodes()).toEqual(false)
  })

  it('getTodosの戻り値があればリストアイテムを表示', async () => {
    mock.onGet('/todos').reply(200, [
      {
        id: 1,
        title: 'title',
        completed: false
      }
    ])

    await act(() => {
      render(<TodoList />)
    })

    expect(screen.getByRole('list').hasChildNodes()).toEqual(true)

    expect(screen.getByRole('listitem').textContent).toEqual('title')
  })
})
```

ここではコンポーネントのレンダリングの前に`act()`を使用しています。
もし気になる方は試しに`act()`を外してテストを実行してみてください。テストが通らないと思います。
これは非同期処理特有の罠で、APIへの応答はMockしているのですが、 APIリクエストの戻り値はPromiseで、それがまだペンディングの状態になっており処理を堰き止めています。 つまりGETのリクエストを投げて、その応答がまだ帰ってきていない状態になっているということです。

このコンポーネントはgetTodosの戻り値に応じてリストを表示しているだけですので、現状このぐらいのテストで十分でしょう。
これでテストが通っていたらひとまず大丈夫です。

## ホーム画面への組み込み

getTodosの戻り値に応じてタスクを表示するコンポーネントはできましたが、肝心の画面への組み込みができていません。
ここではホーム画面への組み込みをやっていきましょう。

### ホーム画面のテスト

ホーム画面を表示したらgetTodosを実行するという処理を実装してみましょう。

```typescript jsx
// Home.test.tsx
import {cleanup, render, screen, waitFor} from '@testing-library/react'
import MockAdapter from 'axios-mock-adapter'
import axios from 'axios'
import Home from '../../pages/Home'
import {act} from 'react-dom/test-utils'
import TodoProvider from '../../context/TodoContext'

describe('Home画面', () => {
  let mock: MockAdapter

  beforeEach(() => {
    mock = new MockAdapter(axios)
  })

  afterEach(() => {
    mock.reset()
    cleanup()
  })

  it('画面構成', async () => {
    mock.onGet('/todos').reply(200, [])

    await act(() => {
      render(<Home />)
    })

    expect(screen.queryByTestId('Header')).toBeTruthy()
    expect(screen.queryByTestId('TodoList')).toBeTruthy()
  })

  it('ホーム画面の初期表示', async () => {
    mock.onGet('/todos').reply(200, [
      {
        id: 1,
        title: 'title',
        completed: false
      }
    ])

    await act(() => {
      render(<TodoProvider><Home /></TodoProvider>)
    })

    expect(mock.history.get[0].url).toEqual('/todos')
    expect(screen.getByText('title')).toBeInTheDocument()
  })
})
```

作成したテストはこのようなものです。元々あったホーム画面の初期表示のテストを拡充しました。

また、APIのテストを実装するときに使用したaxios-mock-adapterが再び登場しました。
本物の値を使用しているのが非常に大事で、要はインテグレーションテストになっています。

### ホーム画面の実装

テストを書いてる際に気づいた方もいるかもしれませんが、ホーム画面とTodoListコンポーネントの役割に重複があります。
今回は以下のようにそれぞれの役割を定義します。
- ホーム画面：
  - Apiを呼び出しタスク一覧を取得する
- TodoListコンポーネント：
  - 取得したタスク一覧を描画する

今回はコンポーネント感でのプロパティの受け渡しに`useContext()`を利用します。
`useContext()`を利用することで、プロパティをグローバルに管理することができます。
また、`useContext()`も`useState()`や`useEffect（）`と同様にReact hooksの一つです。詳しく知りたい方は[こちら](https://ja.react.dev/reference/react/useContext)を参照してください。

実際のコードは以下です。

まず、src配下に`context`ディレクトリを作成し、その中に`TodoContext.tsx`を作成します。

```
src
├── App.tsx
├── components
│    └── 省略
├── context
│    └── TodoContext.tsx  // 作成
├── features
│    └── 省略
├── index.css
├── main.tsx
├── pages
│    └── 省略
└── vite-env.d.ts

```

```typescript jsx
// TodoContext.tsx
import React, {createContext, useMemo, useState} from 'react'
import {Todo} from '../features/TodoApi'

type Props = {
  children: React.ReactNode
}

type InitialState = {
  todos: Todo[]
  setTodos: React.Dispatch<React.SetStateAction<Todo[]>>
}

export const TodoContext = createContext<InitialState | null>(null)

const TodoProvider: React.FC<Props> = ({children}) => {
  const [todos, setTodos] = useState<Todo[]>([])
  const todosValue = useMemo(() => ({todos, setTodos}), [todos, setTodos])
  return (
          <TodoContext.Provider value={todosValue}>{children}</TodoContext.Provider>
  )
}

export default TodoProvider

```

`TodoContext.tsx`では、`TodoContext`と`TodoProvider`の2つを定義しています。

`TodoContext`はTodoの配列をとその中身を設定するための関数を持っています。また、`TodoProvider`は`TodoContext`を子コンポーネントで使用できるようにするため、`TodoContex.Provider`で子コンポーネントをラップする形になっています。

次に、`TodoList.tsx`をTodoContextを使用するように修正します。

```typescript jsx
// TodoList.test.tsx
import {render, screen, waitFor} from '@testing-library/react'
import React from 'react'
import TodoList from '../../src/components/TodoList'

describe('TodoList.tsx Component', () => {
  let todoContextMock: jest.Mock

  beforeEach(() => {
    todoContextMock = React.useContext = jest.fn()
  })

  it('todoContextが空ならリストも空', () => {
    todoContextMock.mockReturnValue({
      todos: []
    })

    render(<TodoList />)

    expect(screen.getByRole('list').hasChildNodes()).toEqual(false)
  })

  it('todoContextがあればリストアイテムを表示', async () => {
    todoContextMock.mockReturnValue({
      todos: [
        {
          id: 1,
          title: 'title',
          completed: false
        }
      ]
    })

    render(<TodoList />)

    expect(screen.getByRole('list').hasChildNodes()).toEqual(true)

    await waitFor(() =>
      expect(screen.getByRole('listitem').textContent).toEqual('title')
    )
  })
})
```

axiosのmockに関する記述は削除し、新たにTodoContextのmockに関する記述を追加しています。

```typescript jsx
// TodoList.tsx
import React from 'react'
import {TodoContext} from '../context/TodoContext'

const TodoList = () => {
  const todoContext = React.useContext(TodoContext)

  return (
    <ul data-testid='TodoList'>
      {Array.isArray(todoContext?.todos) && todoContext.todos.map((todo) => (
              <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  )
}

export default TodoList
```

実装も同様にApi呼び出しを削除し、TodoContextの使用に関する記述を追加しています。 結果として、TodoContextの値のみに依存する非常にシンプルな作りに修正することができました。

次に、`Home.tsx`の修正です。

```typescript jsx
// Home.test.tsx

// 中略

  it('ホーム画面の初期表示', async () => {
    mock.onGet('/todos').reply(200, [
      {
        id: 1,
        title: 'title',
        completed: false
      }
    ])

    await act(() => {
      render(<TodoProvider><Home /></TodoProvider>)     // 修正
    })

    expect(mock.history.get[0].url).toEqual('/todos')
    expect(screen.getByText('title')).toBeInTheDocument()
  })
```

テストでもTodoContextを使用したいので、HomeコンポーネントをTodoProviderでラップします。


```typescript jsx
// Home.tsx
import {useContext, useEffect} from 'react'
import TodoList from '../components/TodoList'
import Header from '../components/Header'
import {getTodos} from '../features/TodoApi'
import {TodoContext} from '../context/TodoContext'

const Home = () => {
  const todoContext = useContext(TodoContext)

  useEffect(() => {
    const fetchData = async () => {
      const response = await getTodos()
      todoContext?.setTodos(response)
    }
    fetchData()
  }, [])

  return (
          <div>
            <Header />
            <TodoList />
          </div>
  )
}

export default Home
```

ホーム画面では、これまでTodoListコンポーネントで実施していたApiの呼び出しと戻り値の格納を担当しています。この際、戻り値の格納先がTodoContextになっていることがポイントです。

これで全てのテストが通る状態になったと思います。

```
 PASS  test/features/TodoApi.test.ts
 PASS  test/components/Header.test.tsx
 PASS  test/components/TodoList.test.tsx
 PASS  test/pages/Home.test.tsx

Test Suites: 4 passed, 4 total
Tests:       6 passed, 6 total
Snapshots:   0 total
Time:        0.973 s, estimated 1 s
Ran all test suites.

Watch Usage: Press w to show more.
```

最後に、`Home.tsx`でTodoContextを使用できるようにするため、`main.tsx`を修正します。

```typescript jsx
// main.tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import './index.css'
import App from './App'
import TodoProvider from './context/TodoContext'

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement)
root.render(
        <React.StrictMode>
          <TodoProvider>    // 修正
            <App />
          </TodoProvider>   // 修正
        </React.StrictMode>
)
```

これでホーム画面への組み込みは完了です。

ここまでのソースコードは
[https://github.com/Onebase-Fujitsu/todo-app-client/tree/step4](https://github.com/Onebase-Fujitsu/todo-app-client/tree/step4)
に置いてあります。

ここは慣れていないと非常に難しい部分だと思いますので、
公式のリファレンスを参考にしながら少しずつトライしてみましょう。

## クライアントの動作確認

さてクライアントの実装は終わりましたが、`npm run dev`を実行して`http://localhost:5173` にアクセスしても、
1日目の実装結果となにも変わりありませんよね。

しかし、このままだとちゃんとクライアントの実装ができてるか少し不安だと思うので、ちょっとしたツールをご紹介します。
[JSON Placeholder](https://jsonplaceholder.typicode.com/) というサービスがあります。

これは予め定義されたJSONを返してくれるサービスで、
例えば [https://jsonplaceholder.typicode.com/todos](https://jsonplaceholder.typicode.com/todos) にアクセスすると、
200件のTodoをJSONで返してくれます。

TodoApi.tsを開いてリクエスト先のURLを一時的に変更してみましょう。

```typescript
// TodoApi.ts

// 中略

export const getTodos = async () => {
  // const response = await axios.get<Todo[]>('/todos')
  const response = await axios.get<Todo[]>('https://jsonplaceholder.typicode.com/todos')
  return response.data
}
```

この状態で再び`http://localhost:5173` を開くと文字列がズラズラ表示されます！

![with jsonPlaceholder](json-placeholder.jpg)

よくみると、これは 
[https://jsonplaceholder.typicode.com/todos](https://jsonplaceholder.typicode.com/todos)
が応答してるjsonのtitle部分になっていることがわかると思います。

正しく処理されていそうですね！

JSON Placeholderは予め定義されているJSONの他にも [自分で作成したJSONを応答させるようにすること](https://my-json-server.typicode.com/) もできます。
サーバの実装は当分先だけどClientだけどうなるか見たいというときはこういったサービスを活用するといいですね。

正しく動作していることが確認できたら再びリクエスト先を/todosに戻しておいてください。
次はサーバの実装をやってみましょう。

---

3日目に続きます

{{< button relref="/docs/practice/day3" >}}3日目{{< /button >}}