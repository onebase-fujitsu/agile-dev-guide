---
title: "リスコフの置換原則(LSP)"
weight: 3
bookToc: true
---

# リスコフの置換原則(The Liskov Substitution Principle)

## リスコフの置換原則(LSP)とはなにか？

リスコフの置換原則(LSP)とは
**派生型はその基本型と置換可能でなければならない**
というものです。
この原則に従わなかったたときどういう事が起こるでしょうか？

ある関数fがある基本クラスBへのポインタ、または、その参照を引数として持っているとしましょう。
その時、Bの派生クラスDがあり、これをBの代わりに関数fにわたすと異常をきたすようなケースを想像してみてください。
この場合、クラスDはリスコフの置換原則に反しているといいます。

## リスコフの置換原則に反する例

### Shapeを派生しないことによる違反

リスコフの置換原則に準じないコードを書こうとするとランタイム型を使うことになります。
そして、ランタイム型を使うとオープン・クローズドの原則に違反したソースコードになってしまいます。

具体例を見てみましょう。

```C++
struct Point {double x,y;};
struct Shape {
    enum ShapeType {square, circle} itsType;
    Shape(ShapeType t) : itsType(t) {}
};

struct Circle : public Shape {
    Circle() : Shape(circle) {};
    void Draw() const;
    Point itsCenter;
    double itsRadius;
};

struct Square : public Shape {
    Square() : Shape(square) {};
    void Draw() const;
    Point itsTopLeft;
    double itsSide;
};

void DrawShape(const Shape& s) {
    if(s.itsType == Shape::square) {
        static_cast<const Square&>(s).Draw();
    else if(s.itsType == Shape::circle) {
        static_cast<const Circle&>(s).Draw();
    }
}
```

この`DrawShape()`は明らかにOCPに違反しています。
`DrawShape()`はShapeクラスのあらゆる派生クラスを知っていなければならず、
新しいShapeクラスの派生型が作られる度に変更を余儀なくされるためです。

ここで、CircleもSquareも独自の`Draw()`を持っています。
しかし、この関数はShapeの関数をオーバーライドしているわけではないので、当然SquareとCircleはShapeの変わりには使えません。
したがって、DrawShapeは受け取ったShapeの型を特定して、適切な`Draw()`を呼ぶ必要があります。

リスコフの置換原則(LSP)に違反している所はどこかというと、SquareとCircleがShapeの代わりとして使えないという部分になります。
そのせいで、DrawShape()はオープン・クローズドの原則にも違反しています。
**リスコフの置換原則に違反すると必然的にオープン・クローズドの原則にも違反してしまうのです。**

### 正方形と長方形の例

先の例は非常に明確に違反している例でしたので、もう少し捉えにくい違反例を紹介します。

```C++
class Rectangle {
    public:
        void SetWidth(double w) {
            itsWidth = w;
        }
        void SetHeight(double h) {
            itsHeight = h;
        }
        double GetHeight() const {
            return itsHeight;
        }
        double GetWidth() const {
            return itsWidth;
        }
    private:
        Point itsTopLeft;
        double itsWidth;
        double itsHeight;
};
```

例えばこのようなRectangle（長方形）クラスを扱うアプリケーションがあるとしましょう。
ここで、ある日長方形だけでなく正方形も扱えるようにしてほしいという要求が出てきたとします。

継承はIS-A（〜〜〜はXXXである）の関係だと言われています。
言い換えると古いオブジェクトと新しいオブジェクトがIS-Aの関係を満たす場合、新しいオブジェクトは古いオブジェクトから派生させるべきものであるということです。

もちろん、正方形は長方形のひとつであるので、したがってSquare(正方形)クラスはRectangle(長方形)クラスから派生するものとみなすのは至って自然です。

{{< mermaid >}}
classDiagram
    Square --|> Rectangle
{{< /mermaid >}}

さて、ここで困ったことが起きてしまったと思います。
SquareはitsWidthとitsHeightも両方は必要とせず、片方しか必要としません。
にも関わらずSquareはRectangleから意味もなくメンバ変数をすべて継承してしまっています。
さらに、それだけでなくSquareはRectangleからsetWidthやsetHeightといった関数も継承してしまっています。
正方形の縦と横の長さは当然同じですから、これらの関数はSquareにとって適切ではありません。

これを解決する方法としてSetWidth()やSetHeight()を以下のようにオーバーライドするとどうでしょうか？

```C++
void Square::SetWidth(double w) {
    Rectangle::SetWidth(w);
    Rectangle::SetHeight(w);
}

void Square::SetHeight(double h) {
    Rectangle::SetHeight(h);
    Rectangle::SetWidth(h);
}
```

こうしておけば、Squareオブジェクトの横の長さを設定したら、それに合わせて高さも変わりますし、同じように縦の長さを設定したら横の長さが変わります。
したがって、Squareの本質は損なわれていないと言えるでしょう。

しかし、以下のような関数を考えてみましょう。

```C++
void f(Rectangle& r) {
    r.SetWidth(32);     // Rectangle::SetWidthが呼び出される
}
```

Squareオブジェクトへの参照をこの関数にわたすとどうなるでしょうか？
ここではRectangle::SetWidthが呼び出されてしまい縦の長さが変わらなくなってしまいます。
つまり、関数fはRectangleの派生型に対して正しく振る舞うことができておらず、リスコフの置換原則に違反していると言えます。

原因はSetWidthとSetHeightが基本クラスRectangleの中でvirtual宣言されていないためで、
ポリモーフィズムのメカニズムが働かないことです。

派生クラスを作ったことで基本クラスに影響が及ぶようなケースは殆どの場合、もともとの設計に不備があったことを暗示しています。
そもそも派生クラスを作ったことで、基本クラスを修正する必要が出てきているということはオープン・クローズドの原則に違反しているということに他なりません。
setWidthやSetHeightを仮想関数にしなかったのは明らかな設計の不備だと言えます。

まずは、それを修正してみましょう。

```C++
class Rectangle {
    public:
        virtual void SetWidth(double w) {
            itsWidth = w;
        }
        virtual void SetHeight(double h) {
            itsHeight = h;
        }
        double GetHeight() const {
            return itsHeight;
        }
        double GetWidth() const {
            return itsWidth;
        }
    private:
        Point itsTopLeft;
        double itsWidth;
        double itsHeight;
};

class Square : public Rectangle {
    public:
        virtual void SetWidth(double w)
        virtual void SetHeight(double h)
};

class Square::SetWidth(double w) {
    Rectangle::SetWidth(w);
    Rectangle::SetHeight(w);
}

class Square::SetHeight(double h) {
    Rectangle::SetHeight(h);
    Rectangle::SetWidht(h);
}
```

これでSquareとRectangleは問題なく動作するようになります。
Rectangleへのポインタやリファレンスを引数としてもつ関数にSquareを渡してもSquareはきちんと正方形として振る舞うようになるでしょう。

では、この設計は矛盾をおこさず正しく動作するでしょうか？

答えは✗で設計自体に自己矛盾がなくても、ユーザにとっては矛盾が生じないとは限らないためです。
以下のような関数gを考えてみてください。

```C++
void g(Rectangle& r) {
    r.SetWidth(5);
    r.SetHeight(4);
    assert(r.Area() == 20);
}
```

この関数はSetWidthとSetHeightをRectangleに属するメンバ関数だと信じてアクセスしています。
そして、この関数はRectangleについては正しく動作しますが、Squareを受け取るとエラーが発生します。
**関数gの作者はRectangleの横の長さを変更しても、縦の長さは変更されないと信じているのです。**

長方形の「横」を変えても「縦」には影響しないと仮定するのは至って合理的です。
しかし、関数gにRectangleとして渡されるものすべてがその過程を満たしているとは限りません。
そうした過程を前提に設計されているgのような関数にSquareのインスタンスを渡すとその関数はご動作してしまいます。
つまり、関数gはSquareとRectangleの継承関係に関して「もろさ」を露呈しているのです。

関数gの問題点は、その作者が長方形の「縦」と「横」が互いに独立していると仮定していることです、
しかし、一方でRectangleと名付けられたクラスの性質として「縦」と「横」を独立して扱えるというのは普遍的なものであることも事実ですし、
その普遍的な性質に従わなかったのはSquareの方であるとも言えます。
しかし、SquareはSquareで正方形の性質にはちゃんと従っています。

## 「IS-Aの関係」に付随する「振る舞い」

SquareとRectangleについて明らかに正しかったにもかかわらず、なぜ不具合がおきて、どこに問題があったと考えるのが正しいでしょうか？

SquareはRectangleはIS-Aの関係になかったのでしょうか？
少なくとも関数gの作者にとってはノーであったはずです。
確かに正方形は長方形かもしれませんが、関数gの視点から見たとき、Squareオブジェクトは確実にRectangleオブジェクトではありません。
というものSquareオブジェクトの振る舞いはRectangleオブジェクトの振る舞いと違うからです。

つまるところ **ソフトウェアとは「振る舞い」そのもの** です。
この「振る舞い」という視点でみるとSquareはRectangleではないと言えます。

SquareとRectangleの関係はこの「振る舞い」の違いのせいでリスコフの置換原則に違反したと言えます。
つまり、 **リスコフの置換原則に準ずるためには「IS-Aの関係」を満たすだけでは不十分で、「振る舞い」の同等性についても注意を払う必要があります。**

## 経験則と約束事

リスコフの置換原則に準じているかどうかを判定できるシンプルな経験則があります。

- 基本クラスからなんらかの機能を省いてしまっているような派生クラス
- 基本クラスにない例外を投げるような派生クラス

このような派生クラスはリスコフの置換原則に反している可能性が高いです。

### 派生クラスに置いて退化している機能

例えば以下のようなケースを考えてみましょう。

```java
public class Base {
    public void f() {
        // なんらかのコード
    }
}

public class Derived extends Bass {
    public void f() {}
}
```

メソッドfはBaseで実装してますが、Derivedの中で省かれてしまってます。
おそらく、Derivedの作者がDerivedの中ではfは何の役割も果たさないと思ったのでしょう。
しかし、Baseのユーザはメソッドfが退化していることを知ることができません。

機能の対価が派生クラスの中で起きているからと言って、必ずしもリスコフの置換原則に反しているとは限りませんが、
そのような状況が起きている場合は注意して見直してみたほうが良いでしょう。

### 派生クラスから例外を投げる

もう一つの違反携帯は基本クラスで投げない例外を派生クラスのメソッドに追加するような場合です。
基本クラスのユーザーがそういった例外を期待していない場合、派生クラスのメソッドに新たな例外を追加してしまうと、基本クラスの代用ができなくなってしまいます。
ユーザーに注意喚起するか、派生クラスが例外を投げないようにしないといけません。

## まとめ

オブジェクト指向設計についてはこれまでいろいろなことが主張されてきていますが、そのすべての確信になるのはオープン・クローズドの原則です。
この原則が有効なときアプリケーションはずっと扱いやすく、再利用可能で頑強なものになります。
リスコフの置換原則はオープン・クローズドの原則を有効にする主要な役割を果たすものの一つです。

このリスコフの置換原則はあるモジュールに修正を施すことなく拡張する方法を、派生型と基本型の置換性というかたちで表現しています。
「IS-A」という用語は派生型の定義として用いるには意味が広すぎます。
派生型の本当の定義は基本型と「置換できる」ということに他なりません。

---

では次に依存性逆転の原則(DIP)について見ていきましょう。

{{% button relref="/docs/softwareDesign/solidPrinciple/dip" %}}依存性逆転の原則{{% /button %}}