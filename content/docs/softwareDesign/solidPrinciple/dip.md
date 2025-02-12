---
title: "依存性逆転の原則(DIP)"
weight: 4
bookToc: true
---

# 依存性逆転の原則(The Dependency Inversion Principle)

## 依存性逆転の原則とは

依存性逆転の原則(DIP)とは

- **上位のモジュールは下位のモジュールに依存してはいけない。どちらのモジュールも「抽象」に依存すべきである。**
- **「抽象」は実装の詳細に依存してはいけない。実装の詳細が「抽象」に依存すべきである。**

というものです。

上位のモジュールが下位のモジュールに依存することは、従来のソフトウェア開発において非常によくあることです。
例えばよくあるWeb三層アーキテクチャを例に上げると、ControllerクラスはServiceクラスに依存しますし、ServiceクラスはRepositoryクラスに依存します。

{{< mermaid >}}
flowchart LR
Controller --> Service
Service --> Repository
{{< /mermaid >}}

それに対して、うまく設計されたオブジェクト指向プログラムの構造は従来の手続き型で見慣れた依存構造を「逆転」したものになります。

上位のモジュールが下位のモジュールに依存するとはどういうことでしょうか。
アプリケーションの方針に基づく重要な判断やビジネスモデルを含み、アプリケーションの存在理由を決定づけているのは上位モジュールです。
それにも関わらず、上位のモジュールが下位のモジュールに依存すると、下位のモジュールの変更が直接上位のモジュールに影響を与え、上位モジュールまで変更を余儀なくされてしまいます。
それはなんとしても避けるべき事態です。

アプリケーションの方針を決めているのは上位レベルのモジュールの方ですので、
実装の詳細を担当している下位モジュールに対して上位モジュールが影響を与えるのが筋でしょう。
ですのでビジネスルールを担当する上位レベルのモジュールは、実装の詳細を担当している下位のモジュールと独立して、立場も上でなければなりません。
いかなる場合も、上位レベルのモジュールが下位のモジュールに依存してはならないということです。

## 階層化

{{< mermaid >}}
flowchart LR
Controller --> Service
Service --> Repository
{{< /mermaid >}}

っこで改めて、先に掲載したWeb三層アーキテクチャの図を見てください。
上位レベルのControllerレイヤが下位レベルのServiceレイヤを使用し、Serviceレイヤは更に下位レベルのRepositoryレイヤを使用しています。
これは一見適切な構造に見えます。
しかし、この構造では上位のControllerレイヤがそれよりも下位のすべてのレイヤにおける変更の影響を受ける、**影響が伝達しやすい依存関係**になっています。

より適切な階層モデルをいかに示します。

{{< mermaid >}}
flowchart BT
    subgraph RepositoryLayer
    Repository --> RepositoryInterface
    end
    subgraph ServiceLayer
    Service --> ServiceInterface
    end
    subgraph ControllerLayer
    Controller --> ControllerInterface
    end
    Repository --> ServiceInterface
    Service --> ControllerInterface
{{< /mermaid >}}

上位レベルの各レイヤでは抽象インタフェースを宣言し、そのインタフェースを通じて必要なサービスを受けるようにします。
下位のレベルのレイヤはこれらの抽象インタフェースを実装するようにします。

こうすれば、上位レベルの各クラスは自ら宣言した抽象インタフェースを通じて、下位レイヤを利用することになるため、
上位のレイヤが下位のレイヤに依存することがなくなります。

逆に、サービスを受けるために上位のレイヤが宣言した抽象インタフェースに依存しているのは、下位のレイヤの方になっています。

## 所有権の逆転

ここで **「逆転」するのは単なる依存関係だけではなく、インタフェースの所有権も逆転する** ことに注意してください。

普通、ユーティリティライブラリのインタフェースはライブラリ自身が所有するものです。
しかし、依存関係逆転の原則を適用する場合、クライアントがその中小インタフェースを所有し、ライブラリのサービスはそのインタフェースから導かれるものだと言えます。

所有権が逆転しているのでControllerレイヤはServiceレイヤやRepositoryレイヤの変更の影響をうけません。
それだけではなく、ControllerレイヤはControllerInterfaceを実装しているモジュールであればどんなものでも再利用できるようになります。
つまり、依存関係を「逆転」することでより柔軟で耐久性と流動性のある構造を生み出すことができます。

## 「抽象」に依存しろ

依存性逆転の原則を一言でいうと「抽象に依存しろ」という経験則だといえます。
この原則は、プログラムは具体的なクラスに依存してはいけないこと、また、プログラム内の関係はすべて抽象クラスかインタフェースで終結すべきであると提言するものです。

- 具体的なクラスへのポインタやリファレンスを保持するような変数があってはならない
- 具体的なクラスから派生するクラスがあってはならない
- 基本クラスで実装されているメソッドを上書きしてしまうようなメソッドがあってはならない

依存性逆転の原則とはすなわち、このような経験則に基づく提言になります。
しかし、この経験則はどのようなプログラムであっても確実に一度は破られることになります。
具体的なクラスのインスタンスは誰かが必ず作らなければなりませんし、結局それを作成したモジュールはそのインスタンスに依存することになるからです。
また、たとえ具体的なクラスであっても頻繁に変更されないクラスについては、この経験則に従う必要はありません。
頻繁に変更されたり、似通った派生クラスが作られることがなければ、プログラムが具体的なクラスに依存していても大した害にはならないからです。

たとえば、文字列を扱うクラスはほとんどのシステムで具体的なクラスです。例えばJavaでしたらStringクラスが具体的な文字列を扱っています。
このクラスは頻繁に変更されるようなことはないので、プログラムがこのクラスに直接依存しても大きな問題にはなりません。

しかし、アプリケーションプログラムの一部として実装される具体的なクラスはたいてい頻繁に変更されるものです。
そうした頻繁に変更される可能性のあるクラスに直接依存することはあってはなりません。

しかし、インタフェースを使った分離も完璧ではありません。
頻繁に変更されやすいクラスのインタフェースを変更しなければならず、その影響がクラスを表現する役割を果たしている抽象インタフェースにまで及ぶ場合もあります。

抽象インタフェースはクライアントクラスが宣言するものであり、それは即ちクライアント自らが必要なサービスを受けるためのものだという視点に立つと、
インタフェースが変更されるのは **クライアントが変更を必要とするときのみ** になります。
この変更はあくまでクライアント主導で行われるもので、抽象インタフェースを実装する立場のクラスがクライアントに影響を与えることはありません。

## 簡単な例

### ボタンと照明

あるクラスが他のクラスにメッセージを送るようなケースでは「依存関係の逆転」が簡単に適用できます。
ボタンと照明を例にして考えてみましょう。

Buttonオブジェクトは外部の状態を感知することができます。ButtonオブジェクトがPollメッセージを受け取ると、ユーザはそれを「押した」かどうかをすぐに判断します。
Buttonオブジェクトが外部の状態を検知するメカニズには、GUIアイコンのボタンでも、実際に指で押すことができるボタンでもなんでも大丈夫です。
Buttonオブジェクトはユーザがそれを起動したのか停止したのかを感知するだけです。

一方Lampオブジェクトは外部に影響を与えます。このオブジェクトはTurnOnメッセージを受け取ると光を放つデバイスにすぐ明かりを灯し、
TurnOffメッセージを受け取ると、その明かりを消します。これも実際に明かりを灯すデバイス実態はLEDだろうが、白熱灯だろうが何でも構いません。

このような制御システムを考えたとき、一般的には以下のようになるでしょう。

{{< mermaid >}}
classDiagram
class Button {
    poll() void
}
class Lamp {
    turnOn() void
    turnOff() void
}
Button ..> Lamp
{{< /mermaid >}}

この例ではButtonオブジェクトはPollメッセージを受け取ってボタンが押されたかどうかを判断し、後は単純にTurnOnかTurnOffメッセージをLampに送るだけです。

しかし、このButtonクラスはLampクラスに直接依存しており、ButtonクラスはLampクラスの変更の影響を受けてしいます。
また、Lampに代わって新たにMotorオブジェクトが出てきたときにButtonを再利用してそれをコントロールすることもできません。
この例ではButtonオブジェクトがコントロールできるのはLampオブジェクトだけだということになります。

つまり、この設計は依存関係逆転の原則に反していると言えます。アプリケーションの上位レベルの方針と下位レベルの実装が分離されていないためです。

### 内在する抽象をみつける

では上位レベルの方針とは何でしょうか？それはアプリケーションに「内在する抽象」であり、実装の詳細が変更されても影響を受けない本質的な部分です。

例えばButtonとLampの例で言えば、ユーザーから受けたon/offの入力を、明かりを灯す物理的な実態に伝達するというのが「内在する抽象」ということになります。
どんなon/offの感知メカニズムを使うのかであったり、どのように灯りをともすのかというのはこれはどんな実態を使っていても大丈夫ですよね。
つまり、内在する抽象とはそういった物によって影響を受けない本質的なものです。

以下の設計はLampオブジェクトへの依存関係を逆転して改善したものです。

{{< mermaid >}}
classDiagram
class Button {
poll() void
}
class ButtonServer {
turnOn() void
turnOff() void
}
class Lamp {
turnOn() void
turnOff() void
}
Button ..> ButtonServer
Lamp ..|> ButtonServer
{{< /mermaid >}}

ここではButtonが依存しているのはButtonServerというインタフェースになります。
Lampはこのインタフェースを実装する立場であり、従って依存される立場ではなく、インタフェースに依存していることになります。
この設計であればButtonはButtonServerインタフェースを実装するデバイスであればどんなデバイスであっても制御することができます。

## 実践的な例

実はここまでのドキュメントを順番に読まれていた方はDIPに準じたアプリケーションの具体的な実装の例を見ています。
[テストダブルの章で使用したサンプルアプリケーション]({{% ref "/docs/softwaretest/testdouble#バックエンドの構造" %}}) を覚えてますでしょうか？

{{< mermaid >}}
classDiagram
class TodoAppController{
TodoAppService todoAppService
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppControllerInterface{
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppService{
TodoAppRepository todoAppRepository
ClockService clockService
getAllTasks() List~Task~
createTask(NewTask newTask) void
finishTask(int taskId) void
revertTask(int taskId) void
deleteTask(int taskId) void
}
class TodoAppServiceInterface{
getAllTasks() List~Task~
createTask(NewTask newTask, Instant now) void
finishTask(int taskId, Instant now) void
revertTask(int taskId, Instant now) void
deleteTask(int taskId, Instant now) void
}
class TodoAppRepository{
JdbcTemplate jdbcTemplate
getAllTasks() List~Task~
createTask(NewTask newTask, Instant now) void
finishTask(int taskId, Instant now) void
revertTask(int taskId, Instant now) void
deleteTask(int taskId, Instant now) void
}
class ClockServiceInterface{
now() Instant
}
class ClockService{
now() Instant
}
TodoAppController ..> TodoAppControllerInterface
TodoAppService ..|> TodoAppControllerInterface
TodoAppService ..> TodoAppServiceInterface
TodoAppRepository ..|> TodoAppServiceInterface
TodoAppService ..> ClockServiceInterface
ClockService ..|> ClockServiceInterface
{{< /mermaid >}}

ちょっと複雑かもしれませんがDIPに準じた実装になっているのが分かりますでしょうか？

具体的なソースコードを見てみましょう。

```java
// TodoAppController.java
@RestController
public class TodoAppController {
    @Autowired
    TodoAppControllerInterface todoAppService;

    // 具体的な実装
}
```

TodoAppControllerクラスはTodoAppControllerInterfaceに依存していて、Interfaceを通じてServiceを利用しています。
TodoAppControllerが依存しているインタフェースは以下のような実装になっていました。

```java
// TodoAppControllerInterface.java
public interface TodoAppControllerInterface {
    List<Task> getAllTasks();
    void createNewTask(NewTask newTask);
    void finishTask(int taskId);
    void revertTask(int taskId);
    void deleteTask(int taskId);
}
```

ではServiceはどの様になっていたかというと、TodoAppServiceはTodoAppControllerInterfaceの実装になっています。

```java
// TodoAppService.java
@Service
class TodoAppService implements TodoAppControllerInterface {
    @Autowired
    TodoAppRepository todoAppRepository;
    
    @Override
    public List<Task> getAllTasks() {
        // 具体的な実装
    }

    @Override
    public void createNewTask(NewTask newTask) {
        // 具体的な実装
    }

    @Override
    public void finishTask(int taskId) {
        // 具体的な実装
    }

    @Override
    public void revertTask(int taskId) {
        // 具体的な実装
    }

    @Override
    public void deleteTask(int taskId) {
        // 具体的な実装
    }
}
```

Repositoryも同様ですね。TodoAppServiceはTodoAppServiceInterfaceに依存していて、このInterfaceを通じてRepositoryを利用しています。
依存しているInterfaceの宣言はこのようになっていました。

```java
// TodoAppServiceInterface.java
public interface TodoAppServiceInterface {
    List<Task> getAllTasks();
    void createNewTask(NewTask newTask, Instant now);
    void finishTask(int taskId, Instant now);
    void revertTask(int taskId, Instant now);
    void deleteTask(int taskId, Instant now);
}
```

そして、その宣言を具体的な実装クラスが実装しています。

```java
// TodoAppRepository.java
@Repository
class TodoAppRepository implements TodoAppServiceInterface {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @Override
    public List<Task> getAllTasks() {
        // 具体的な実装
    }

    @Override
    public void createNewTask(NewTask newTask, Instant now) {
        // 具体的な実装
    }

    @Override
    public void finishTask(int taskId, Instant now) {
        // 具体的な実装
    }

    @Override
    public void revertTask(int taskId, Instant now) {
        // 具体的な実装
    }

    @Override
    public void deleteTask(int taskId, Instant now) {
        // 具体的な実装
    }
}
```

どのレイヤも具体的な実装に直接依存しておらず、Interfaceに依存していることがわかると思います。

## まとめ

伝統的な手続き型のプログラミングでは、方針が実装の詳細に依存する構造になってしまいます。
このような構造だと「方針」が詳細の変更に影響されてしまうので好ましくありません。

オブジェクト指向プログラミングでは方針と実装の詳細を共に抽象に依存させることで、依存関係を逆転させることができます。
この依存関係の逆転が織り込まれていることこそ、いいオブジェクト指向設計の顕著な特徴だと言えます。
依存関係が逆転していればオブジェクト指向設計ですし、依存関係が逆手飲していなければ手続き型設計であるということができます。

依存関係逆転の原則はオブジェクト指向技術のさまざまな利益を享受するために必要不可欠なものです。
再利用可能なフレームワークを構築するためにはこの原則の適切な適用が欠かすことができません。
この原則を適用することで抽象と実装の詳細は完全に切り離されますので、コードを保守することがずっと楽になります。

---

それでは最後にインタフェース分離の原則(ISP)について見てみましょう。

{{% button relref="/docs/softwareDesign/solidPrinciple/isp" %}}インタフェース分離の原則{{% /button %}}