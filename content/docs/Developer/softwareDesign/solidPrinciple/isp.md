---
title: "インターフェース分離の原則(ISP)"
weight: 5
bookToc: true
---

# インターフェース分離の原則(The Interface Segregation Principle)

## インタフェース分離の原則とは

インターフェス分離の原則(ISP)とは **クライアントにクライアントが利用しないメソッドへの依存を強制してはいけない** という原則です。
この原則は「太った」インタフェースをうまくシェイプアップしてくれるものです。
「太った」インタフェースを持つクラスは多数のインタフェースを抱え込んでおり、部分的にしかインタフェース同士が強い関連性を持っていません。
このことは、視点を変えると強い関連性があるインタフェース同士をまとめてグループ化できるということです。
グループ化されたメンバ関数は、それぞれ異なるクライアントにサービスを提供することになります。
なぜなら、クライアントによって必要なメンバ関数は異なるからです。

一般にクライアントは複数のインタフェースを利用しますが、その全てが互いに強い関連性を持っているわけではありません。
インタフェース分離の原則はすべてのインタフェースを１つのクラスに押し込めてしまうのではなく、
関連性を持ったインタフェースはグループ化し、抽象基本クラスとして利用すべきだという主張になります。

## インタフェース汚染

次のようなセキュリティシステムを考えてみます。このシステムにはDoorオブジェクトがあり、それをロックしたり解除したりすることができます。
また、ドアが開いているのか閉じているのかを知ることもできます。

```java
public abstract class Door {
    public abstract void Lock();
    public abstract void Unlock();
    public abstract boolean IsDoorOpen();
}
```

これは抽象クラスなので、クライアントはこのDoorインタフェースを実装しているオブジェクトであればどのようなオブジェクトでも使うことができます。

さて、このインタフェースを実装したクラスの一つであるTimedDoorを見てみましょう。
TimedDoorは長時間ドアが開放されていると警報がなるドアです。この機能を実現するために、TimedDoorはTimerというオブジェクトと通信します。

```java
public abstract class Timer {
    public abstract void Register(int timeout, TimerClient client);
}

public interface TimerClient {
    void Timeout();
}
```
タイムアウトを警告してほしい場合、オブジェクトはTimerのRegisterメソッドを呼び出します。
引数はタイムアウトの時間の長さと、TimerClientになっており、タイムアウトしたときにTimeOut()がコールバックされるようになっています。

ここで、TimerClientクラスがTimedDoorにタイムアウトを通知するにはどうしたらいいでしょうか？

{{< mermaid >}}
classDiagram
Timer --> "0..*" TimerClientInterface
Door ..|> TimerClientInterface
TimedDoor --|> Door
class TimerClientInterface {
    TimeOut() void
}
{{< /mermaid >}}

ここで示す方法では、強制的にTimerClientからDoorを派生させています。
つまり、TimedDoorもTimerClientから派生していることになり、こうすることで、TimedDoorは自分自身を登録し、かつ自分自身がTimeOutのメッセージを受け取ることができるようにしています。

この方法には問題があり、特にDoorクラスがTimerClientに依存していることが問題です。
Doorの派生クラスすべてがTimerを必要とするわけではありません。
実際、最初に記載したDoorの抽象クラスではTimerを必要としておらず、
Timerを必要としないDoorの派生クラスではTimeOutメソッドを使わない（退化させる）ことになり、これは潜在的にリスコフの置換原則に違反しているということになります。

また、Doorの派生クラスを使うアプリケーションでは、たとえTimerを使わなくてもTimerClientの定義を必ずインポートする必要があり、
この方法には「不必要な複雑さ」や「不必要な繰り返し」があることが分かります。

これがインタフェース汚染です。こういった症状はJavaやC++のように静的に片付けされた言語に共通に発生する可能性があります。
この例ではDoorインタフェースが本来不要なメソッドTimeOutで汚染されてしまっているというわけです。
このメソッドをDoorに組み込まなければならない理由はDoorの派生型の一つに恩恵を与えるためでしかありません。
こういった事が行われると、ある派生型が新しいメソッドを必要とする度に、不要なメソッドを基本クラスに追加することになってしまいまいます。
そうすると、基本クラスのインタフェース汚染はますます拡大し、インタフェースが「太る」ことになります。

また、新しいメソッドを基本クラスに追加する度に、派生クラスでも追加されたメソッドを実装しなければならなくなります。
こうした面倒を解消する方法の一つに、そういったメソッドは基本クラスでは単純な実装をしておき、派生クラスでは必要がなければ何もしないという方法があります。
しかし、こうした手法はリスコフの置換原則に違反するため、保守や再利用の際に問題をきたすことになります。

## クライアントの分離とインタフェースの分離

本当はDoorとTimerClientは全く異なるクライアントにインタフェースを提供すべきで、
TimerがTimerClientを使うべきですし、ドアを操作するクラスがDoorを使うべきです。

### クライアントがインタフェースに与える強制力

ソフトウェアをどうしても変更しなければならない場合、インタフェースの変更がユーザにどのような影響を及ぼすかを考えるのが自然です。
例えば、TimerClientのインタフェースを変更した場合、その変更によってTimerClientのユーザが受ける影響を憂慮します。

しかし、それとは逆にユーザがインタフェースの変更を強いる場合があります。

例えばTimerのユーザが１つ以上のタイムアウトを登録するとします。
この場合、TimedDoorはどうなるでしょうか？Doorが開いたことを検出すると、TimerにRegisterメッセージを贈り、タイムアウトの報告を要求します。
しかし、タイムアウトの前にドアが締まり、しばらく閉まった後に、再度開いたとします。
この場合、古いタイムアウトが時間切れになる前に、新たなタイムアウトをリクエストする必要があります。
こうしてTimerには2つのタイムアウトのリクエストが発行されたことになりますが、最終的に最初にリクエストしたタイムアウトの方が先に時間切れになり、
TimedDoorのTimeOutメソッドを呼び出してしまいます。すなわち、Doorは誤ったタイミングで警報を鳴らしてしまうことになるのです。

以下に示すような約束事を使えばこの状況を修正することができます。

```java
public abstract class Timer {
    public abstract void Register(int timeout, int timeoutId, TimerClient client);
}

public interface TimerClient {
    void TimeOut(int timeOutId);
}
```

各々のタイムアウトを登録する度にtimeOutIdという固有のコードを導入し、TimerがTimerClientのTimeOutを呼び出すときに、同じコードを引数として渡すようにします。
こうしておけば、TimerClientの派生型はどのタイムアウトが応答したのかを知ることができます。

この変更がTimerClientのすべてのユーザーに影響を与えるのは明白です。
しかし、ここではそれを受け入れることにします。というのもtimeOutIdが無いのは明らかに見落としであり、修正が必要だからです。

{{< mermaid >}}
classDiagram
Timer --> "0..*" TimerClientInterface
Door ..|> TimerClientInterface
TimedDoor --|> Door
class TimerClientInterface {
TimeOut() void
}
{{< /mermaid >}}

しかし、このような設計になっていると、この修正の影響がDoorにまで及んでしまいます。
この設計には「硬さ」と「扱いにくさ」があるということです。TimerClientのバグがTimerを使わないDoorの派生型を利用するクライアントにまで影響をおよぼすのはおかしいと言わざるを得ません。

## クラスインタフェースとオブジェクトインタフェース

さて、この例をインタフェースの分離という角度から再考してみましょう。
2つのクライアント(TimerとDoorのユーザ)は分離した2つのインタフェースを持つオブジェクトを1つ利用するようにします。
この2つのインタフェースは同一のデータを操作するので、必ず同一オブジェクトに実装しなければならないからです。

ではどうしたらインタフェース分離の原則に準じて、両者が共存しなければならない状況で、インタフェースを分離することができるでしょうか？

それについては、「移譲」を使ったり、そのオブジェクトの「基本クラス」を通してオブジェクトにアクセスしたりすることで、
オブジェクトのインタフェースをを通してオブジェクトにアクセスしないことで解決を図ります。

### 移譲を使ったインタフェースの分離

解決策の一つはTimerClientから派生するオブジェクトを作り、具体的な処理はTimedDoorに移譲する方法です。

{{< mermaid >}}
classDiagram
class TimerClientInterface {
    +TimeOut
}
class DoorTimerAdapter {
    +TimeOut()
}
class TimedDoor {
    +DoorTimeOut
}
Timer --> "0..*" TimerClientInterface
DoorTimerAdapter --|> TimerClientInterface
DoorTimerAdapter --> TimedDoor
TimedDoor ..> DoorTimerAdapter : creates
TimedDoor ..> Timer
TimedDoor --|> Door
{{< /mermaid >}}

```java
public class TimedDoor extends Door {
    public TimerClient getDoorTimerAdapter() {
        return new DoorTimerAdapter(this);
    }
    // 処理省略
}

public class DoorTimerAdapter implements TimerClientInterface {
    TimedDoor itsTimedDoor;
    
    DoorTimerAdapter(TimedDoor timedDoor) {
        itsTimedDoor = timedDoor;
    }
    
    public void TimeOut() {
        itsTimedDoor.DoorTimeOut();
    }
}
```

TimedDoorがTimerを使ってタイムアウトのリクエストを登録したいときは、DoorTimerAdapterを作り、それをTimerに登録する。
TimerがTimeOutメッセージをDoorTimerAdapterに送ると、DoorTimerAdapterはそのメッセージ処理をTimedDoorに移譲する。

この解決方法はDoorクライアントとTimerの結合が防がれているため、インタフェース分離の原則に準じていると言えます。
そのため、Timerに変更があったとしても、Doorのユーザは誰ひとりとして影響を受けることはありません。
また、TimedDoorのインタフェースはTimerClientのものと全く同じである必要はありません。
DoorTimerAdapterがTimerClientのインタフェースをTimedDoorのインタフェースに翻訳してくれるためです。
したがって、これは非常に柔軟な解決策になっていると言えます。

しかし、この方法はタイムアウトを登録するたびに新たなオブジェクトを生成しなければならずあまりいい方法ではありません。

### 多重継承を使って分離する方法

以下に多重継承をつかってインタフェース分離の原則を実現する方法を示します。

{{< mermaid >}}
classDiagram
class TimerClientInterface {
+TimeOut
}
class TimedDoor {
+TimeOut
}
Timer --> "0..*" TimerClientInterface
TimedDoor --|> TimerClientInterface
TimedDoor --|> Door
TimedDoor ..> Timer
{{< /mermaid >}}

```java
public class TimedDoor extends Door implements TimerClientInterface {
    abstract void TimedOut(int timeOutId);
}
```

ここではTimedDoorがDoorとTimerClientInterfaceの両方を継承しています。
どちらのクライアントもTimedDoorを利用できますし、TimedDoorに依存していません。
どちらのクライアントも分離したインタフェースを通して、同一のオブジェクトを利用することができます。


## まとめ

「太った」クラスはそれを利用するクライアント同士が有害な関連性をもつ原因になりえます。
クライアントが「太った」クラスに変更を強いると、他のすべてのクライアントに影響が及んでしまうからです。

この有害な関連性を断ち切るには、各クライアントは実際に自分が利用するメソッドだけに依存するようにします。
「太った」クラスのインタフェースを各クライアントのニーズに合うようにインタフェースを細かくグループ化すればそれが可能になります。

クライアントごとに特化したインタフェースにはそれを利用するクライアントやクライアントグループだけが利用するメソッドだけを宣言しておきます。
後で「太った」クラスを構築したければ、クライアント毎に特化したインタフェースをすべて継承し、必要なインタフェースを実装したら良いです。

このような方針を取ることで、クライアントは自分が利用しないメソッドとの依存関係を断ち切りクライアント同士の独立性を保つことができます。