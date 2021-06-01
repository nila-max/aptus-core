# Aptus

"Aptus" is latin for suitable, appropriate, fitting. It is a utility library to help smooth certain pain points of the Java/Scala ecosystem.

It is currenly used as part of [Gallia](https://github.com/galliaproject/gallia-core/blob/master/README.md#210129170214), a Scala library for data manipulation.

<a name="210121153149"></a>
**aptus-core** dependency graph:<br/><br/>
<div style="text-align:center"><img src="./dependencies.png" alt="core dependency graph"></div>

<a name="210531095628"></a>
A big motivation for including all these dependencies is that I find myself constantly adding them to my projects, in order to get things done.
A great example of this being a method like `splitByWholeSeparatorPreserveAllTokens` (from `org.apache.commons.lang3.StringUtils`) whose semantics feel more intuitive to me than the `String.split`'s.

## SBT
`libraryDependencies += "io.github.aptusproject" %% "aptus-core" % "0.2.0"`

Then import the following:

```scala
import aptus._ // or more specific imports, eg import.aptus.String_
```

The library is available for both scala 3.0, 2.13 and 2.12

## Examples

#### In-line printing
<a name="210531093421"></a><a name="printing"></a>
```scala
"hello".p               // prints: "hello"
"hello".p.toUpperCase.p // prints: "hello", then "HELLO"

3.str     .p // prints "3"
3.toString.p // prints "3"
```
Convenient for quick debugging

#### In-line assertions
<a name="210531093422"></a><a name="in-line-assertions"></a>
```scala
  "hello".assert (_.startsWith("h"))                    .toUpperCase.p // prints "HELLO"
  "hello".assert (_.startsWith("h"), x => s"value=${x}").toUpperCase.p // prints "HELLO"    
  "hello".require(_.startsWith("h"))                    .toUpperCase.p // prints "HELLO"   
//"hello".assert (_.startsWith("H"))                    .toUpperCase.p // throws AssertionError
//"hello".assert (_.startsWith("H"), x => s"value=${x}").toUpperCase.p // throws AssertionError:
                                                                       //   assertion failed: value=hello
```
Convenient for chaining, consider the alternative:
```scala
{
  val str = "hello"
  assert(str.startsWith("h"))
  str.toUpperCase.p
}
```

#### String operations
<a name="210531093423"></a><a name="string-ops"></a>
```scala
"hello". append(" you!")  .p // prints "hello you!"
"hello".prepend("well, ") .p // prints "well, hello"
"hello".colon             .p // prints "hello:"
"hello".colon  ("human")  .p // prints "hello:human"
"hello".tab    ("human")  .p // prints "hello<TAB>human"
"hello".newline("human")  .p // prints "hello<new-line>human"
"hello".quote             .p // prints "\"hello\""
"hello|world".splitBy("|").p // prints List(hello, world)
// .. many more, see String_
```


#### Conditional piping (a.k.a thrush)
<a name="210531093424"></a><a name="conditional-piping"></a>
```scala
"bonjour".pipeIf(_.startsWith("h"))(_.toUpperCase).p // prints "bonjour" (unchanged)
"hello"  .pipeIf(_.startsWith("h"))(_.toUpperCase).p // prints "HELLO"

3.pipeIf(_ % 2 == 0)(_ + 1).p // prints 3 (unchanged)
4.pipeIf(_ % 2 == 0)(_ + 1).p // prints 5

val suffixOpt = Some("?")
"hello".pipeOpt(suffixOpt)(suffix => _ + suffix).p // prints "hello?"
"hello".pipeOpt(None)     (suffix => _ + suffix).p // prints "hello" (unchanged)
```

See [discussion](https://users.scala-lang.org/t/implicit-class-for-any-and-or-generic-type/501) on _Scala Users_.

#### In-line "to Option"
<a name="210531093425"></a><a name="in-line-to-option"></a>
```scala
"hello"  .as.noneIf(_.size >  5).p // prints Some("hello")
"bonjour".as.noneIf(_.size >  5).p // prints None

"hello"  .as.someIf(_.size <= 5).p // prints Some("hello")
"bonjour".as.someIf(_.size <= 5).p // prints None
```

Convenient for chaining, consider the alternative:
```scala
{
  val str = "hello"
  val opt =
    if ("hello".size > 5) None
    else                  Some(str)
  opt.p
}
```

#### "force" disambiguator (Option/Map)
<a name="210531093426"></a><a name="force-disambiguator"></a>

`.get` is polysemic in the standard library, sometimes "attempting" to get the result as with `Map` (returns `Option[T]`), sometimes "forcing" it as with `Option` (returns `T`)
   
aptus' `.force` better conveys semantics unambiguously:

```scala
val myOpt = Some("foo")
val myMap = Map("bar" -> "foo")

myOpt.get         .p // prints "foo" -> stdlib way, forcing 
myOpt.force       .p // prints "foo"
myMap.get  ("bar").p // prints Some("foo") -> stdlib way, attempting
myMap.force("bar").p // prints "foo"
```

### Handling files
<a name="210531093427"></a><a name="files-handling"></a>
```scala
"~/.bashrc".readFileLines().head.p
  // on my machine, prints:
  //   "# ~/.bashrc: executed by bash(1) for non-login shells."
  
Seq("hello", "world").writeFileLines("/tmp/hello") // self-explanatory
"/tmp/hello".readFileContent().p                   // prints "hello<new-line>world"
```

### WIP
<a name="210531093428"></a>
(lots more to port here)

