---
title: "How Do I Use Cake Pattern in T-Code-Engine"
date: 2026-05-09T13:45:12-07:00
weight: 5
tags:
    - Coding
    - Scala
categories: ["Blog"]
url: "/posts/how-do-i-use-cake-pattern/"
logical_breadcrumbs:
    - title: "Blog"
      url: "/categories/blog"
---

T-Code-Engine is a project to build a Japanese IME for T-Code input method, which does:

- (Direct input) Takes key strokes and generates corresponding Japanese characters (e.g. `kd` => `の` and `hj` => `連`)
- (Kanji composition) Combines multiple Japanese characters and provides
  combined characters based on rules (e.g. `工` + `頭` => `項`)
- (Mixed conversion) Converts into phrases in Kanji/Kana from Kanji/Kana mixed segment (e.g. `き者` => `記者`)

Also, T-Code-Engine is designed for multi platform, the abstraction is
paramount. In this article, we will explore how to use the Cake pattern to
achieve abstraction and ensure the ease of testing with working example, rather
than classic example with service and repository.

## Abstraction of Keyboard Layouts

First of all, the variety of keyboards can be an issue since T-Code is based on
key strokes, regardless of what letter is printed on each key. For example, the
key of "Q" on Qwerty keyboard should have exactly the same effect to the key of
"D" on Dvorak keyboard. It is a great place to start with abstraction over
keyboard layouts.

The most straightforward way to implement the direct input is to create a map
that takes two key positions and generate a character. For historical reason we
call it a character table, that takes indices which stands for two key
positions. We define a trait named `Strokes` to represent this mapping.

```scala
abstract trait Strokes {
  def getChar(first: Int, second: Int): Char
}
```

For different keyboard layouts, we can generate different indices from keyboard
characters:

```scala
abstract trait Layout{
  val keyseq: Seq[Char]
  def getStroke(c: Char): Option[Int] = {
    if (keyseq.contains(c)) {
      Some (keyseq.indexWhere(_ == c))
    } else {
      None
    }
  }
  def getKey(i: Int): Option[Char] = {
    if (i<40){
      Some(keyseq(i))
    } else None
  }
}
```

Then we can supply Qwerty and Dvorak layouts as:

```scala
/** Provides validation of Qwerty keys stroke for T-Code */
trait QwertyLayout extends Layout{
  val keyseq = "1234567890qwertyuiopasdfghjkl;zxcvbnm,./"
}
/** Provides validation of Dvorak key stroke for T-Code */
trait DvorakLayout extends Layout{
  val keyseq = "1234567890\',.pyfgcrlaoeuidhtns;qjkxbmwvz"
}
```

Since the implementation of `Strokes`(which maps indices to Japanese characters)
and each subclass of `Layout` (which maps keyboard characters to indices) are
completely isolated, they can be tested individually.

The Cake Pattern implementation brings us the flexibility for Engine
implementation to switch between different `Layout`:

```scala
class Engine { self: Layout =>
  def convert(chars: Array[Char]): Array[Char] = ...
}

val q_engine = new Engine with QwertyLayout
val d_engine = new Engine with DvorakLayout
```



## Abstraction of Dictionaries Source

Beyond keyboard layouts, another critical area requiring abstraction is how the
engine accesses its underlying data source.

T-Code input method is based on three kinds of dictionaries. The first one has
been mentioned in the previous section, character table. The remaining two are:

- *mazegaki* dictionary for mixed conversion
- *bushu* dictionary for kanji composition

These two dictionaries can be rather large compare to the character table.
Initially, I implemented these as a Zip archive of text file and read them when
creating instance of `MixedConversion` and `Combinator` classes. This works fine
for a text input work load.

However, unlike desktop IMEs, **Android IMEs have a very short lifecycle** and
are not persistent background processes. The system may recreate the IME service
frequently—such as when a user taps a text box or switches apps. Loading a full
file buffer into memory during these frequent restarts is expensive. By using
the Cake Pattern, we can swap in a `SQLiteCombinatorDictionary` for Android.
This allows the engine to establish a quick database connection instead of
parsing a flat file, significantly reducing startup latency.

For kanji composition feature, we need two functions. They corresponds to
composition and decomposition:

```scala
abstract trait CombinatorDictionary{
  def findComposition(a: Char, b: Char): Option[Char]
  def findParts(result: Char): Array[(Char, Char)]
}
```

For reading Zip archive dictionary, we just read for the archive and parse, then
store it as `HashMap`.

```scala
trait ArchivedCombinatorDictionary extends CombinatorDictionary {
  val dictionary: HashMap[(Char, Char), Char] = ArchivedCombinatorDictionaryFactory.readDictionary()
  val undictionary: HashMap[Char, Array[(Char, Char)]] = dictionary.toArray.groupMap(_._2)(_._1).to(HashMap)

  override def findComposition(a: Char, b: Char): Option[Char] = {
    dictionary.get(a, b) orElse dictionary.get(b, a)
  }
  override def findParts(result: Char): Array[(Char, Char)] = {
    undictionary.getOrElse(result, Array[(Char, Char)]())
  }
}
object ArchivedCombinatorDictionaryFactory{
  def readDictionary(): HashMap[(Char, Char), Char] = {...}
}
```

For dictionary stored in SQLite database, first we have to check for the
existence of the database. If it is found, we connect to it and run SQL while
operating for `findComposition` and `findParts`. Otherwise, read Zip archive and
create the database file.

The initialization parameters for the database path is rather for testing, and
`jdbc_prefix` is for Android system since it uses native implementation of
`jdbc:sqldroid` when creating the database connection, rather than standard
`jdbc:sqlite`.

```scala
trait SQLiteCombinatorDictionary(jdbc_prefix: String, bushu_path: String) extends CombinatorDictionary{

  private var connection: java.sql.Connection = uninitialized
  private val dbfile = new File(bushu_path)
  if(!dbfile.exists()){
    extractResource("/tcode_dict.zip", dbfile)
  }

  connection = java.sql.DriverManager.getConnection(s"${jdbc_prefix}:${dbfile.getAbsolutePath}")

  private def extractResource(resourceName: String, destination: java.io.File): Unit = {}
  override def findParts(result: Char): Array[(Char, Char)] = {
    val sql = "SELECT char1, char2 FROM bushu WHERE result = ?"
    ...
  }
  override def findComposition(a: Char, b: Char): Option[Char] = {
    val sql = "SELECT result FROM bushu WHERE (char1 = ? AND char2 = ?) LIMIT 1"
    ...
 }
}
```

After implementing the character table and mixed conversion feature with
dictionary access implemented in quite similar manner with kanji composition, we
can mix these together:

```scala
abstract class InteractiveEngine extends Strokes { this: Layout =>
  val mixed: MixedConverter
  val combi: Combinator
  ...
}

class ArchivedInteractiveEngine extends InteractiveEngine with ArchivedStrokes { this: Layout =>
  val mixed = new MixedConverter with ArchivedMixedConverterDictionary
  val combi = new Combinator(this) with ArchivedCombinatorDictionary
  ...
}

class SQLiteInteractiveEngine(
  jdbc_prefix: String,
  tcode_tbl_path: String,
  mazegaki_path: String,
  bushu_path: String
) extends InteractiveEngine with SQLiteStrokes(jdbc_prefix, tcode_tbl_path) { this: Layout =>
  val mixed = new MixedConverter with SQLiteMixedConverterDictionary(jdbc_prefix, mazegaki_path)
  val combi = new Combinator(this) with SQLiteCombinatorDictionary(jdbc_prefix, bushu_path)
  ...
}
```

Thus, these variants of engines instances can be created via:

```scala
val a_engine = new ArchivedInteractiveEngine with QwertyLayout
val s_engine = new SQLiteInteractiveEngine(jdbc_prefix, tcode_tbl_path, mazegaki_path, bushu_path) with QwertyLayout

```

## Testing

The flexibility from using the Cake Pattern also gives us the flexibility of testing.
For each injection, we can write different fixtures:

```scala
trait ArchivedInteractiveEngineFixture { self: FunSuite =>
  val archivedEngine = new Fixture[ArchivedInteractiveEngine] ("interactive engine"){...}
  override def afterEach = ...
}
trait SQLiteInteractiveEngineFixture { self: FunSuite =>
  val sqliteEngine = new Fixture[SQLiteInteractiveEngine] ("interactive engine"){...}
  override def afterEach = ...
}
```

Then we can run the same test across different implementations via mix-ins of fixtures:

```scala
abstract class InteractiveEngineTestBase[T <: InteractiveEngine] extends munit.FunSuite {
  def engine: Fixture[T]
  override def munitFixtures = List(engine)

  test("記しゃ"){
    val ie = engine()
    "fjyijstt".foreach(ie.put(_))
    ie.inflexRight()
    ie.convert()
    ie.selectCandidate(0)
    val res = ie.commit()
    assert( clue(res) == (clue("記者")))
  }
  ...
}
class SQLiteInteractiveEngineTest extends InteractiveEngineTestBase[SQLiteInteractiveEngine] with SQLiteInteractiveEngineFixture {
  override val engine = sqliteEngine
}
class ArchivedInteractiveEngineTest extends InteractiveEngineTestBase[ArchivedInteractiveEngine] with ArchivedInteractiveEngineFixture {
  override val engine = archivedEngine
}
```

By abstracting the engine's dependencies, we've turned a platform-specific
hurdle into a pluggable component that remains fully testable in a local JVM
environment.

## Conclusion

Balancing the performance constraints of Android with the development ease of a
desktop environment is a common challenge in multi-platform projects. By
leveraging the Cake Pattern, T-Code-Engine maintains a clean separation between
core logic and platform-specific data access.

We moved from a "one-size-fits-all" approach to a system where:

- Desktop remains lightweight and simple using Zip-based dictionaries.
- Android gains the necessary speed through SQLite's indexed lookups.
- Testing remains unified, ensuring that a change in the engine logic works across all implementations.

While the Cake Pattern is often criticized for its complexity in larger systems,
here it provides exactly the compile-time safety and modularity needed to handle
the unique lifecycles of different operating systems.
