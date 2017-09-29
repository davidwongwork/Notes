# Lambdas, Functional

## Integrating with Java lambdas
Here is an example of an implicit conversation from a Java lambda that takes a parameter of type `T` and returns `void` to a Scala function of the same signature (`T => Unit`).
The Scala function that is returned simply takes an instance of type `T` and calls the `accept` method of the Java `Consumer` lambda.
```scala
implicit private def j2sLambda(f: java.util.function.Consumer[T]): T => Unit =
  (t: T) => f.accept(t)
```
This allows us to define a Scala curry method like:
```scala
def a(s: String)(f: T => Unit): Unit = {
  ...
  val t: T = ...
  f(t)
  ...
}
```
But would allow Java clients to call the Scala method with:
```java
...
a("hello", t -> {
  t.something();
  ...
});
```

