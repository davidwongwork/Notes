Explanation from https://www.scala-lang.org/blog/2016/12/07/implicit-function-types.html

Implicits in Scala allow passing of parameters into a method without having to
explicitly writing the argument to the method in code. This usually makes sense
when you have parameters that you're passing to your method just so you can pass
it again to a method that you're calling and you're not really using that parameter
directly yourself.

The mechanism to do so is to label the parameter as implicit in the method definition.
The caller of the method must also label a var/val/def as being implicit and that var/val
must be of the same type as expected by the method. Labeling the var/val/def is the only
way to disambiguiate any other var/val/def's you may have that is also of the same type.

Implicit def's are a special case where the method definition doesn't need to label its
parameter as implicit and the caller would explicitly call the method with the argument.
However, the argument can be a different type than what is asked for by the method and
your implicit def needs to be a function that takes the caller type and outputs a matching
type the method is asking for. Your def will be implicitly called and its result is passed
to the method.

Here is an example of using an implicit val:
```scala
def transaction[T](op: Transaction => T) = {
    val trans: Transaction = new Transaction
    op(trans)
    trans.commit()
}


def f1(x: Int)(implicit thisTransaction: Transaction): Int = {
    thisTransaction.println(s"first step: $x")
    f2(x + 1)
}

def f2(x: Int)(implicit thisTransaction: Transaction): Int = {
    thisTransaction.println(s"second step: $x")
    f3(x * x)
}
```
Calling code must label the param implicit so that it can be passed to f1 automatically (implicitly)
```scala
transaction {
    implicit thisTransaction =>
        val res = f1(args.length)
        println(if (thisTransaction.isAborted) "aborted" else s"result: $res")
}
```
The definition of f1 and f2  are cumbersome because you have to repeat the 
implicit param definition for each function f1, f2, ... This becomes very cookie cutter.

An equivalent definition of f1 is this:
```scala
def f1(x: Int) = { implicit thisTransaction: Transaction =>
    thisTransaction.println(s"first step: $x")
    f2(x + 1)
}
```
This is called an Implicit Function Type.

We moved the implicit param to the definition side of the function so that
it is part of f1's type information instead of in its param list. There is a reason
for this given below, but take a moment to consider the following:

The first definition of f1 was typed:   `(Int)(Transaction)=>Int`
<br/>
The second definition is typed:         `(Int)(implicit Transaction)=>Int`

Remember that lambas (e.g. A=>B) is really scala.Function1[A,B] and its magic is in its "apply" method
Likewise, implicit A=>B is really scala.ImplicitFunction1[A,B].

The syntax sugar conversions are generated by the compiler and so the generated "apply" method might be:
```scala
apply(implicit $ev0: Transaction) : Int
```
Since $ev0 is a generated name for the parameter at compile time, you can write code that references it.
In order to access the parameter, you must use "val x = implicitly[T]" to assign it a name of your own.

The reason to create a Implicit Function is to make the implicit marker part of the type information.
From that, we can short hand the method definition by providing an alias to the type
```scala
type Transactional[T] = implicit Transaction => T
```
Instead of using Int, you can use generics (type param T)

So then, we can redefine f1, f2 as:
```scala
def f1(x: Int): Transactional[Int] = {
    val thisTransaction = implicitly[Transaction]
    thisTransaction.println(s"first step: $x")
    f2(x + 1)
}

def f2(x: Int): Transactional[Int] = {
    val thisTransaction = implicitly[Transaction]
    thisTransaction.println(s"second step: $x")
    f3(x * x)
}
```
There is a small slight of hand here where `(Int)(implicit Transaction)=>Int` is now equivalent to 
`(Int)=> implicit Transaction=>Int`

This is just from currying: i.e. if you apply `f1(Int) _` you will be left with a function that takes 
`implict Transaction` and produces an `Int`

This looks cleaner and gives the act of implicitly passed args a name; e.g. "Transactional"
