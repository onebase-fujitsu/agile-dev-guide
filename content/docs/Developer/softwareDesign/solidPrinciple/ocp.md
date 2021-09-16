---
title: "オープン・クローズドの原則(OCP)"
weight: 2
bookToc: true
---

# オープン・クローズドの原則(The Open Closed Principle)

## オープン・クローズドの原則(OCP)とはなにか？

オープン・クローズドの原則(OCP)とは
**ソフトウェアの構成要素(クラス、モジュール、関数など)は拡張に対して開いて(オープン)いて、修正に対しては閉じて(クローズド)いなければならない**
という原則です。

「硬い」設計にしてしまうと、プログラムのちょっとした変更でさえ、その箇所と依存関係を持つすべてのモジュールに影響を与えてしまいます。
オープン・クローズドの原則を適用することで、そうした修正の影響範囲を小さくできます。

オープン・クローズドの原則に従って設計されたモジュールには次のような特徴があります。

1. **拡張に対して開かれている**  
これはモジュールの振る舞いを拡張できるという意味です。
アプリケーションの仕様変更があったとしても、モジュールに新たな振る舞いを追加することでその変更に対処できるようにします。
つまり、そのモジュールの変更内容を自由に変更できるということです。

2. **修正に対して閉じている**  
モジュールの振る舞いを変更しても、そのモジュールのソースコードやバイナリコードはまったく影響を受けない。
すでにモジュールがコンパイルされてバイナリ形式になっているものは、それがリンクライブラリであれ、DLLであれ手を触れる必要はないということです。

一見この2つの特徴は矛盾しているように見えます。
通常モジュールの振る舞いを拡張するにはそのモジュールのソースコード自体を変更しなければなりません。
また、ソースコードを変更しなければそのモジュールの振る舞いを変更できないと思うのが普通だと思います。

ではどうしたら、ソースコードを変更せずにモジュールの振る舞いを変えることができるでしょうか？

## 『抽象』に依存しろ

C++やJavaのようなオブジェクト指向言語は宣言が固定されていても、それが特定の実装に結合していないメソッドを「抽象」を使って表現できます。
これらの言語では「抽象」は抽象基本クラスを使って記述され、特定の実装に結合していないメソッドはその派生クラスで実装されます。

モジュールの設計ではこういった「抽象」のメカニズムを利用することが有効です。
モジュールをある固定した「抽象」に依存させておけば、修正に対してコードを閉じることができるからです。
抽象を使えば、コードを修正しなくても、抽象派生のクラスを新たに追加するだけでモジュールの振る舞いを拡張することができます。

{{<mermaid>}}
classDiagram
Client --> Server
{{< /mermaid >}}

この例ではClientがServerの両方の実装が「具体的に」実装されてしまっています。
ClientクラスはServerクラスを利用しているので、Clientオブジェクト別のServerオブジェクトを利用することに慣ればClientクラスを変更する必要があります。

{{<mermaid>}}
classDiagram
Client --> ClientInterface
Server ..|> ClientInterface
{{< /mermaid >}}

そこで上の図のような設計にしてみるとどうでしょうか。
ClientInterfaceクラスは抽象クラスであり、抽象メンバ関数をいくつか持っています。
Clientクラスは、この抽象クラスを利用することになります。
しかし、実際にClientクラスが利用するのは抽象クラスから派生したServerクラスのオブジェクトになります。
つまり、Clientオブジェクトが新しいServerクラスを利用したくなったら、ClientInterfaceから派生したクラスを別に追加したらいいわけです。
Clientクラスそのものを変更する必要はありません。

## 具体例

### OCPに従わない図形描画の実装

まず、OCPに従わない実装を見てみましょう。

2つの構造体CircleとSquareは最初のデータ要素`ShapeType itsType;`のみ共通で、他の要素は異なっています。
`ShapeType itsType;`で図形の型を示し、それによって円なのか四角なのかを判断できる仕組みになってます。
`DrawAllShapes()`メソッドでは構造体へのポインタの配列を順次呼び出し、
最初に型を調べてから、その方に応じて必要な関数(`DrawCircle()`や`DrawSquare()`)を呼び出してます。

```C++
// shape.h
enum ShapeType {circle, square};

struct Shape {
    ShapeType itsType;
};
```

```C++
// circle.h
struct Circle {
    ShapeType itsType;
    double itsRadius;
    Point itsCenter;
}

void DrawCircle(struct Circle*);
```

```C++
// square.h
struct Square {
    ShapeType itsType;
    double itsSide;
    Point itsTopLeft;
}

void DrawSquare(struct Square*);
```

```C++
// drawAllShapes.cc
typedef struct Shape *ShapePointer;

void DrawAllShapes(ShapePointer list[], int n) {
    int i;
    for (i=0; i<n; i++) {
        struct Shape* s = list[i];
        switch (s -> itsType) {
            case square:
                DrawSquare((struct Square*)s);
                break;
            case circle:
                DrawCircle((struct Circle*)s);
                break;
        }
    }
}
```

この`DrawAllShapes()`は明確にオープン・クローズドの原則に反しています。
新しい種類の図形を定義して、それを描画したいときに、`DrawAllShapes()`も修正が必要です。
つまり、この関数は修正に対して閉じていません。

このプログラムは簡単な例に過ぎず、図形の描画処理を呼び出しているだけなので、新しい図形を追加するときに変更する箇所は1箇所で済んでいます。
しかし、実際にアプリケーションを組むとなると、図形を移動したり、拡大したり、変形したりといった様々な処理を呼び出すことになります。
そうした処理をおこなうときに描画の処理と似たようなSwitch文による分岐処理が必要になってきます。
そうすると、新しい図形を1種類追加するときにそうしたSwitch文による分岐処理を行っているところすべてに変更が必要になってくるわけです。

他にも問題があります。新しい図形の種類を追加しようとするとどんな変更が必要になるでしょうか？

まず、列挙型のShapeTypeに新しい図形を登録しなければなりません。
そうすると、扱っているすべての図形がこの列挙型の宣言に依存しているので、すべての図形を再コンパイルし直さなければいけませんし、
同様の理由でShapeに依存するモジュールも再コンパイルが必要になってきます。

したがって、ソースコードのswitch/case文やif/else文の部分をすべて変更するだけでなく、構造体Shapeを使うすべてのモジュールのバイナリファイルも再コンパイルが必要になってきます。
バイナリファイルの変更はDLLや共有ライブラリだけでなく、それ以外のバイナリコンポーネントを再結合し直さなければならないということを意味しています。

結局新しい図形をアプリケーションに追加するという単純な行為のために、様々なモジュールのソースが変更になるだけでなく、
モジュールのバイナリファイルやライブラリコンポーネントまでもすべて変更されてしまいます。

また、このプログラムは移植性が非常に悪いです。`DrawAllShapes()`を他のプログラムから利用しようとすると、
別に必要もないSquareやらCircleまでもがオマケでひっついてきてしまいます。

### OCPに従った図形描画の実装

以下に示すコードは、先に示したコードの課題をOCPに従うことで解決しているものです。
ここではまずShapeという名前の抽象クラスを宣言していて、その中で`Draw()`という抽象メソッドを一つだけ持つようにしています。
そしてCircleとSquareはこのShapeから派生したクラスになっています。

```java
public abstract class Shape {
    public abstract void Draw();
}

public abstract class Square extends Shape {
    double itsSide;
    Point itsTopLeft;
}

public abstract class Circle extends Shape {
    double itsRadius;
    Point itsCenter;
}

public class DrawingTool {
    public void DrawAllShapes(List<Shape> shapes) {
        for (Shape shape : shapes) {
            shape.Draw();
        }
    }
}
```

ここで新しい図形を追加したければ、Shapeから派生させた新しいクラスを作成すればいいですよね。
`DrawAllShapes()`には全く手を加えなくていい、つまり、`DrawAllShapes()`はオープン・クローズドの原則に準じているということを意味しています。

実用的なアプリケーションでは先に前述したように、移動や変形と言った処理も必要になってきます。
しかし、そういった場合でも新しい図形を追加するのは非常に簡単で、新しい図形の派生クラスを作って必要な処理をすべて実装するだけです。
アプリケーションをくまなく見直して、変更が必要な箇所を探し回る必要はありません。

また、移植性も担保されてます。どんなアプリケーションが`DrawAllShapes()`を使っても余計なオマケを考慮しなくていいからです。

## 落とし穴

では [先の例](#OCPに従った図形描画の実装) において**四角形を描画する前にすべての円を描画しなければならない**というように仕様を変更したらどうでしょうか？

`DrawAllShapes()`はそうした変更には閉じてません。
この変更に対応するには`DrawAllShapes()`の中で、まずCircleのリスト検索を行ってその描画処理を行ってから、
同様にSquareのリスト検索を行ってその描画処理をするといった感じで`DrawAllShapes()`内部の処理の変更が必要になってきます。

## 先を見越した構造と自然な構造

このような仕様変更が起きることを先に見越していれば、その変更から身を守る別の「抽象」を導入できたはずです。
しかし、[先の例](#OCPに従った図形描画の実装) で使った抽象はこの種の変更に対して助けになるどころか邪魔でしかありません。
つまり、この抽象は不適切だったということです。

一見、基本クラスShapeを使ったモデルは極々自然なものに見えます。
しかし、図形の形よりも描画の順番のほうが重要なシステムに於いてはShapeを使ったモデルは自然ではなかったということになります。

つまり、どんなに「閉じた」モジュールであっても、閉じることのできない変更というのはありえます。
**すべてのケースにおいて適用できる自然なモデルなど存在しません。**

あらゆる変更に対して完璧に閉じることが不可能なら、戦略的に閉じていく必要があります。
つまり、設計者がどういった種類の変更に対して自分の設計を閉じたいのかを選択する必要があります。
設計担当者はどういった種類の変更が頻繁にあるのかを推測し、そういった変更から自分を守れるように抽象を構築していかなければなりません。

ではどうすれば、発生しそうな変更を推測できるでしょうか？
それがユーザリサーチだったり仮説検証のプロセスだったりするわけです。
アジャイル開発では今後どういったことを開発するべきかを仮説します。
ユーザにヒアリングしたり、プロトタイプを触ってもらって次に実装する機能を決定します。
そうした、一連のプロセスからどういった変更がありそうかを事前に見越しておくことしか我々にできることはありません。

## 明示的に閉じる

### 優先付けを図形から取り外すアプローチ

さて、現に`四角形を描画する前にすべての円を描画しなければならない`という要求が出てきたときにどうすればいいでしょうか？
閉じるという行為は抽象を使うことで実現できます。つまり、順番に対して`DrawAllShapes()`を閉じるためには、順番の抽象化が必要になってきます。
順番を抽象化し、あらゆる種類の順序付けを記述できるようにすればよさそうです。

順序付けができるということは2つのオブジェクトが与えられたとき、どちらを先に描画すべきなのかを見つけられるということを意味しています。
そこで`Precedes(優先する)`という名前の抽象メソッドをShapeに定義して順序付けを実現してみましょう。

```java
public abstract class Shape {
    abstract void Draw();
    abstract boolean Precedes(Shape shape);
}

public class ShapeComparator implements Comparator<Shape> {
    public int compare(Shape shape1, Shape shape2) {
        if (shape1.Precedes(shape2)) {
            return -1;
        } else {
            retrun 1;
        }
    }
}

public class DrawingTool {
    void DrawAllShapes(List<Shape> shapes) {
        List<Shape> orderedShapes = DrawingOrderSort(shapes);
        for (Shape shape : orderedShapes) {
            shape.Draw();
        }
    }
    
    List<Shape> DrawingOrderSort(List<Shape> shapes) {
        List<Shape> orderedShapes = new ArrayList<Shape>(shapes);
        Collection.sort(orderedShapes, new ShapeComparator());
        return orderedShapes;
    }
}
```

ここではComparatorクラスのcompareをオーバーライドすることによって順序付けを行うメソッドを実現しました。
しかし、このままでは個々のShapeオブジェクトは`Precedes()`メソッドをオーバーライドしてどういった順序付けをするのかを指定する必要があります。

例えば、Circleの`Precedes()`メソッドを書いてみるとこのようになります。

```java
public abstract class Circle extends Shape {
    double itsRadius;
    Point itsCenter;

    boolean Precedes(Shape shape) {
        return shape instanceof Square;
    }
}
```

この`Precedes()`はオープン・クローズドの原則に反しているのは個々までの説明からも明らかでしょう。
もちろん頻繁に新規の図形が定義されないのであればこれでも構いませんが、頻繁に追加されるようだとこのままだと難しいのは火を見るより明らかです。

### テーブル駆動のアプローチを使う

個々の図形が互いの存在を知らなくてもいいようにShapeの派生型を閉じてみましょう。
ここではテーブル駆動形のアプローチを使ってみます。

```java
public abstract class Shape {
    abstract void Draw();
    abstract String getType();
    private String[] typeOrderTable = {"square", "circle"};
    
    boolean Precedes(Shape shape) {
        String thisType = this.getType();
        String argType = shape.getType();
        
        int thisOrd = -1;
        int argOrd = -1;
        int ord = 0;
        
        for (String tableEntry : typeOrderTable) {
            if (tableEntry.equals(thisType)) {
                thisOrd = ord;
            }
            if (tableEntry.equals(argType)) {
                argOrd = ord;
            }
            if ((0 <= argOrd) && (0 <= thisOrd)) {
                break;
            }
            ord++;
        }
        return thisOrd < argOrd;
    }
}
```

ここではShapeに要素の順番を定義する配列を作成しました。

```private String[] typeOrderTable = {"square", "circle"};```

というのがそれです。
このアプローチを使えば一般的な順序付け問題に対してDrawAllShapesを閉じることができますし、
それだけでなくShapeの派生型クラスを作成したときも、各Shapeの派生型のソースコードを変更する必要はなくなります。

この方法を採用した場合、様々な図形の順序付けに対して閉じていないのはこの配列のみということになります。
このテーブルは他のモジュールから切り離し、テーブル自身を独立したモジュールにしてしまうのがよいでしょう。


## まとめ

オープン・クローズドの原則はオブジェクト指向設計の核心であり、この原則に従うことで、オブジェクト指向技術から得られる利益（柔軟性。再利用性、保守性）を最大限に享受できるようになります。
しかし、オブジェクト指向プログラミング言語を使えば自動的にオープン・クローズドの原則に準拠できるかというと、そうではないですし、
また、アプリケーションのあらゆる部分で抽象をむやみに使えばいいわけでもありません。

開発者が担当する部分で最も頻繁に変更される処理を幹訳、抽象を適切に適用していく必要があります。
**早まった「抽象」をしないことも、「抽象」を使うのと同じように重要です。**

---

では次にリスコフの置換原則(LSP)について見ていきましょう。

{{< button relref="/docs/softwareDesign/solidPrinciple/lsp" >}}リスコフの置換原則{{< /button >}}