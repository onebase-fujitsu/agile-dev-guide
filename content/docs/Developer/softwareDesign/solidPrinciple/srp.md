---
title: "単一責任の原則(SRP)"
weight: 1
bookToc: true
---

# 単一責任の原則(The Single Responsibility Principle)

## 単一責任の原則(SRP)とはなにか？

単一責任の原則(SRP)とは**クラスを変更する理由は1つ以上存在してはならない**という原則です。

例えば以下に示すような設計を考えます。

{{<mermaid>}}
classDiagram
Computational Geometry Application --> Rectangle
Graphical Application --> Rectangle
Graphical Application --> GUI
Rectangle --> GUI
class Rectangle {
    +void draw()
    +double area()
}
{{< /mermaid >}}

Rectangleクラスには2つのメソッドがあり、1つはスクリーンに四角形を描き、もう一つは四角形の面積を計算します。
ここではRectangleクラスを2つのアプリケーションが使っています。

アプリケーションの1つは図形のジオメトリ（骨格）を計算するアプリケーションで、
このアプリケーションは図形のジオメトリを演算処理するためにRectangleクラスを使用しますが、特にスクリーンに表示する機能は使いません。

もう一つのアプリケーションはグラフィクスを担当し、ジオメトリの計算もするが、基本的には四角形を画面に描画する用途でRectangleを使います。
この設計は単一責任の原則に反しています。というのも、Rectangleクラスがジオメトリの計算と画面への描画の2つの役割を担ってしまっているためです。
四角形のジオメトリの数学的なモデルを提供する役割と、GUIの四角形を描画する役割を担っています。

こうしたSRPの違反は様々な問題を引き起こします。

例えば、これがジオメトリの計算を扱うアプリケーションがコンソールアプリケーションだとしたら、
不要なGUIがリンクされてしまうので、リンク時間やコンパイル時間が、メモリ空間の浪費に繋がります。

それ以上に問題なのが、グラフィックアプリケーションの変更が原因でRectangleクラスを変更することになった場合、
本来グラフィックとは無関係なはずのジオメトリアプリケーションをリビルト、再テスト、再ロードをしないといけなくなります。

これを防ぐためにはRectangleクラスを以下のように2つに分け、Rectangleのジオメトリの演算処理をGeometricRectangleクラスに任せてしまうことです。
こうしておけば、四角形を塗りつぶす方法に変更が生じてもジオメトリの計算を扱うアプリケーションは影響を受けずにすみます。

{{<mermaid>}}
classDiagram
Computational Geometry Application --> Geometric Rectangle
Graphical Application --> Rectangle
Graphical Application --> GUI
Rectangle --> GUI
Rectangle --> Geometric Rectangle
class Rectangle {
    +void draw()
}
class Geometric Rectangle {
    +double area()
}
{{< /mermaid >}}

## 役割とはなにか？

単一責任の原則では **「役割(責任) = 変更理由」** と定義しています。
クラスを変更するのに2つ以上の理由がある場合、そのクラスには2つ以上の役割があるということになります。
しかし、クラスが2つ以上の役割を担っているかどうかを見極めるのは非常に難しい場面も多くあります。

例えば以下のようなModemインタフェースを考えてみましょう。

```java
interface Modem {
    void dial(String pno);
    void hangUp();
    void send(char c);
    char recieve();
}
```

ほとんどの人が合理的なインタフェースだと判断すると思います。
ここで宣言されている4つの関数が「モデムの機能」であることが明らかであるためです。

しかし、`dial()`メソッドと`hangUp()`メソッドは「接続の管理」を担っており、`send()`メソッド`とrecieve()`メソッドは「データ通信」を担っています。

ではここで、この2つの機能を以下のように分離すべきかどうかというと、それは**アプリケーションが今後どのように変更されるかどうかによります。**

{{<mermaid>}}
classDiagram
ModemImplementation ..|> DataChannel
ModemImplementation ..|> Connection
class DataChannel {
    +void send(char);
    +char recieve();
}
class Connection {
    +void dial(String);
    +void hangUp();
}
{{< /mermaid >}}

接続を管理する部分がアプリケーションの変更の影響を受ける場合、このようにinterfaceを分離しておけば、
不要なリコンパイルや再ロードを避けることができます。

一方で、2つの役割が必ず同時に変更されるようなケースでは、これらを必ずしも分離する必要はありません。
**分離してしまうとかえって設計が不必要に複雑になってしまうためです。**

ここで改めてSRPの定義を見てほしいのですが、単一責任の原則とは**クラスを変更する理由は1つ以上存在してはならない**というものです。
つまり**変更の兆候もないのに単一責任の原則を適用するのは懸命ではない**ということです。

## 永続性のあるシステムと単一責任の原則

例えば以下のような設計があったとします。

{{<mermaid>}}
classDiagram
Employee --> Persistence Subsystem
class Employee {
    +int calculatePay()
    +void store()
}
{{< /mermaid >}}

これは明確な単一責任の原則違反の例で、従業員クラスがビジネスルールと永続性のあるシステム（データベースなど）を内包してしまっています。

永続性のあるシステムはあまり変化しませんが、ビジネスルールは途中で変化するもので、これらを結合してはいけません。

## まとめ

単一責任の原則(SRP)は最もシンプルな原則のひとつですが、正しく適用することが難しい原則の1つでもあります。
Modemの例のように私達は何かに付けて複数の役割を結合してしまいがちだからです。
結合している役割を見つけて、それらを分離する作業はソフトウェア設計の本質的なものになります。

---

では次にオープン・クローズドの原則(OCP)について見てみましょう。

{{< button relref="/docs/softwareDesign/solidPrinciple/ocp" >}}オープン・クローズドの原則{{< /button >}}