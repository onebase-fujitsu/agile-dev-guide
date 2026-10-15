---
title: "Day 6"
weight: 6
bookToc: true
---

# Day 6

Now that the client and server work together to create tasks, let's write scenario tests.

## Setting Up the E2E Test Environment

First, let's create an environment for E2E tests.

```shell
mkdir e2e
cd e2e
mkdir test
touch test/test.js 
npm init
```

When you run the `npm init` command, a setup dialog appears. For now, just press Enter for everything without setting anything, and create package.json.
Then install testcafe, an E2E testing library.

```shell
npm install --save-dev testcafe
```

Once the installation is complete, edit package.json as follows.

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

After editing, run the following command in the e2e directory.
If Chrome starts, a screen like the one in the image appears for a moment, and then it closes, your environment is ready.

```shell
npm run test
```

![testcafe](testcafe.jpg)


The repository up to this point is available at
[https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step12](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step12)
.

## Writing E2E Tests

Let's start writing E2E tests right away. First, let's check that the top page is displayed.
Change test.js as follows.

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

Then, **with both the client app and the server app running**, run the `npm run test` command.
If everything is fine, the test should pass.

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

Once the test passes, add another test to check that a task can be added.

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

This should also pass without any problems when you run it.

The repository up to this point is available at
[https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step13](https://github.com/onebase-fujitsu/todo-app-vite/tree/feature/step13)
.

## Toward Scaling the App Further

So far, we have built a simple SPA from scratch with test-driven development, connected it to a backend API,
and reached the point of testing the whole process end to end with E2E tests.

However, the granularity of how we split Components is still vague.
When several people develop together, the granularity of Components inevitably varies from developer to developer.
You might even end up with a huge Component without intending to.
This is where we want to bring in the ideas of [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/).

App development offers an enormous amount of freedom. In agile development, decisions about development are left to the developers,
so you have to choose the best approach from countless development methods, decide on the architecture, and implement it.

And the more freedom there is, the more the "variation" in development style and skill between individuals becomes a problem.
**The most important thing in team development is conventions.**
In this hands-on, the very first thing we did was configure eslint. Conventions really matter.

Always build consensus within the team, such as "this is how we split components" and "this is how we design backend APIs,"
and for anything that can be checked automatically, like lint, encode the conventions into lint to maintain code quality.

If there are members who cannot maintain code quality, it is a good idea to hold study sessions within the team.
And in the first place, members who cannot maintain code quality should not join the project at that point.
They should receive training beforehand and join the project once they reach a certain skill level.

We still haven't implemented the backend update API `PATCH /todos/{id}`, and there is still a mountain of work to do.
**There is no end to agile app development.**
Please keep working on delivering even more value to users.

---

Continue to the wrap-up.

{{% button relref="/docs/inClosing" %}}In Closing{{% /button %}}
