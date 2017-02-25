mpromise API for Scala.js
================================
[mpromise](https://www.npmjs.com/package/mpromise) - A mpromise library for NodeJS.

### Description

`mpromise` is a password hashing function designed by Niels Provos and David Mazières, 
based on the Blowfish cipher, and presented at USENIX in 1999.

### Build Dependencies

* [ScalaJs.io v0.3.x](https://github.com/scalajs-io/scalajs.io)
* [SBT v0.13.13](http://www.scala-sbt.org/download.html)

### Build/publish the SDK locally

```bash
 $ sbt clean publish-local
```

### Running the tests

Before running the tests the first time, you must ensure the npm packages are installed:

```bash
$ npm install
```

Then you can run the tests:

```bash
$ sbt test
```

### Examples

A simple example:

```scala
import io.scalajs.nodejs._
import io.scalajs.npm.mpromise._

val promise = new Promise[(Int, Int)]()
promise.onFulfill { case (a, b) =>
  Assert.equal(3, a + b)
}
promise.fulfill((1, 2))
```

Supports asynchronous execution pipelines:

```scala
import io.scalajs.nodejs._
import io.scalajs.npm.mpromise._

val promise = new Promise[Int]()
promise
    .`then`(arg => arg + 1)
    .`then`(arg => throw new Error(arg + " is an error!"))
    .`then`(null, { err =>
      Assert.ok(err.isInstanceOf[Error])
      Assert.equal("2 is an error", err.message)
})
promise.fulfill(1)
```

Supports conversion to `Future`s.

```scala
import io.scalajs.npm.mpromise._
import scala.scalajs.concurrent.JSExecutionContext.Implicits.queue

val promise = new Promise[(Int, Int)]()
promise.fulfill((2, 3))

promise.toFuture foreach { case (a, b) =>
  println(s"result: ${a + b}") // 5
}
```

Supports promise chaining:

```scala
import io.scalajs.npm.mpromise._
import scala.scalajs.concurrent.JSExecutionContext.Implicits.queue

def makeMeAPromise(i: Int) = {
    val p = new Promise[Int]()
    p.fulfill(i)
    p
}

val initialPromise = new Promise[Int]()
var returnPromise = initialPromise
for (i <- 0 until 10) {
  returnPromise = returnPromise.chain(makeMeAPromise(i))
}

initialPromise.fulfill(1)
returnPromise.toFuture foreach { result =>
  println(s"returnPromise: $result") // 9
}
```

### Artifacts and Resolvers

To add the `mpromise` binding to your project, add the following to your build.sbt:  

```sbt
libraryDependencies += "io.scalajs.npm" %%% "mpromise" % "0.5.5"
```

Optionally, you may add the Sonatype Repository resolver:

```sbt   
resolvers += Resolver.sonatypeRepo("releases") 
```