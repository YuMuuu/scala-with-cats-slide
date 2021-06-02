---
theme: Scala With Cats
paginate: true
marp: true
size: 16:9
---
# Scala with Cats

---
# 序文

プログラム設計を構造化する手法として、Monad、Functor、などの関数型プログラミングパターンの紹介とCatsでの実装を説明します

Monadとそれに関連する概念は、オブジェクト指向のデザインパターン以上の正確なの表現力があります

Generality(一般性)を理解することでMonadのような概念を色々な状況に適用できるます

---
# 環境

## version

```
scalaVersion := "2.13.1"

libraryDependencies +=
  "org.typelevel" %% "cats-core" % "2.1.0"

scalacOptions ++= Seq(
  "-Xfatal-warnings"
)
```

## Template Projects

```
$ sbt new scalawithcats/cats-seed.g8
```

---
# 1 Introduction
Catsにはさまざまな関数型プログラミング ツールが含まれています。これらのツールの大部分は、既存の Scala 型に適用できる型クラスの形式で提供されます

型クラスは Haskell2 を参考にして作られています

これにより、継承を使用せず、元のソースコードを変更せず、既存のライブラリを新しい機能で拡張できます


まず最初に　CatsのShow型クラスとEq型クラスの例を見て基礎を学習します

---
# 1.1 Anatomy of a Type Class　（型クラスの構造）
型クラスには3つの重要なコンポーネントがあります

型型クラス、型クラスのインスタンス、型クラスを使用するメソッドです

Scala 言語の構成要素は、次のように型クラスのコンポーネントに対応しています。

- traits: 型クラス
- implicit values: 型クラスのインスタンス
- implicit parameters: 型クラスを使用するメソッド
- implicit classes: ユーリティなど

---
# 1.1.1 The Type Class (型クラス)
型クラスは、実装したい機能を表すインターフェースまたは API です
Scala では、型クラスは少なくとも 1つの型パラメーターを持つ特性によって表されます（？）
たとえば次のように一般的な「JSON にシリアライズ化」を表現する事ができます

```
// Define a very simple JSON AST
sealed trait Json
final case class JsObject(get: Map[String, Json]) extends Json
final case class JsString(get: String) extends Json
final case class JsNumber(get: Double) extends Json
final case object JsNull extends Json

// The "serialize to JSON" behaviour is encoded in this trait
trait JsonWriter[A] {
  def write(value: A): Json
}
```

---
# 1.1.2 Type Class Instances (型クラスのインスタンス)

型クラスのインスタンスは、関心のある特定の型の型クラスの実装を提供します
型クラスの具体的な実装を作成し暗黙のキーワードでタグ付けすることによってインスタンスを定義します

```
final case class Person(name: String, email: String)

object JsonWriterInstances {
  implicit val stringWriter: JsonWriter[String] =
    new JsonWriter[String] {
      def write(value: String): Json =
        JsString(value)
    }

  implicit val personWriter: JsonWriter[Person] =
    new JsonWriter[Person] {
      def write(value: Person): Json =
        JsObject(Map(
          "name" -> JsString(value.name),
          "email" -> JsString(value.email)
        ))
    }
}
```

---
# 1.1.3 Type Class Use(型クラスの利用)

型クラスの使用とは、型クラスのインスタンスが機能することを必要とする機能です。 WIP
