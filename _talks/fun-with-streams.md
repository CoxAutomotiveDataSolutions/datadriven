---
title: "Fun with Scala Streams"
authors:
 - Alex Bush
 - Alex Bush
 - Alex Bush
image:
  path: /images/streams.png
  thumbnail: /images/streams.png
theme: night
description: A brief talk about using the Stream collections in Scala, and how they make solving Mathematical problems a piece of cake
---

<section data-markdown data-separator-vertical="===" data-separator="---" >
	<textarea data-template>

# Fun with Scala Streams ⛲

Featuring Project Euler

---

## What are Streams?

A Stream is like a list except that its elements are computed lazily: 💤

```scala
scala> val str = 1 #:: 2 #:: 3 #:: Stream.empty
str: scala.collection.immutable.Stream[Int] = Stream(1, ?)
```

===

A Stream can be infinitely long, only those elements requested are computed: 🔁

```scala
scala> Stream.continually(1).take(10).toList
res4: List[Int] = List(1, 1, 1, 1, 1, 1, 1, 1, 1, 1)
```


===

**Even the transformations on Streams are Streams🔱**

```scala
scala> Stream.from(1).take(10)
res5: scala.collection.immutable.Stream[Int] = Stream(1, ?)

scala> Stream.from(1).takeWhile(_ < 10)
res6: scala.collection.immutable.Stream[Int] = Stream(1, ?)

scala> Stream.from(1).map(i => i * i)
res9: scala.collection.immutable.Stream[Int] = Stream(1, ?)
```

---

## What is Project Euler? 👨‍🏫

A series of mathematical problems intended to be solved with computer programs

===

### Example Problems

* **Problem 1:** Find the sum of all the multiples of 3 or 5 below 1000.

* **Problem 19:** How many Sundays fell on the first of the month during the twentieth century (1 Jan 1901 to 31 Dec 2000)?

* **Problem 2:** By considering the terms in the Fibonacci sequence whose values do not exceed four million, find the sum of the even-valued terms.

---

## Constructing Streams (1) 🏗️

```scala
scala> Stream.continually(1)
res10: scala.collection.immutable.Stream[Int] = Stream(1, ?)

scala> Stream.from(1)
res11: scala.collection.immutable.Stream[Int] = Stream(1, ?)

scala> Stream.from(1, 2)
res12: scala.collection.immutable.Stream[Int] = Stream(1, ?)
```

===

### Problem 1

Find the sum of all the multiples of 3 or 5 below 1000.

```scala
scala> Stream.from(1)
             .takeWhile(_ < 1000)
             .filter(i => i % 5 == 0 || i % 3 == 0)
             .sum
res13: Int = 233168
```

===

## Constructing Streams (2) 🏗️

Created by an initial state and a function continuously applied:

```scala
scala> Stream.iterate
   def iterate[A](start: A)(f: A => A): Stream[A]

scala> Stream.iterate(1)(_ + 1).take(10).toList
res18: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

===

### Problem 19

How many Sundays fell on the first of the month during the twentieth century (1 Jan 1901 to 31 Dec 2000)?

```scala
scala>
import java.time.{LocalDate, DayOfWeek}

Stream.iterate(LocalDate.parse("1901-01-01"))(_.plusDays(1))
      .takeWhile(_.getYear < 2001)
      .count(d => d.getDayOfMonth == 1 && 
                  d.getDayOfWeek  == DayOfWeek.SUNDAY)

res29: Int = 171
```


---

## How do they work? 🤔

The `from` function is defined as a recursive function:

```scala
def from(start: Int, step: Int): Stream[Int] =
  cons(start, from(start+step, step))
```

===

So successive items will look like:

```scala
cons(start, cons(start, cons(start, from(start+step, step))))
```


===

Looking into the definition of `cons`:

```scala
def apply[A](hd: A, tl: => Stream[A]) = new Cons(hd, tl)
```

The second argument is defined as *call-by-name* (or is lazily evaluated)

>**`t1` is only evaluated when it is needed**

---

## Constructing Streams (3) 🏗️

Created using `cons`:

```scala
// Definition of cons
// def apply[A](hd: A, tl: => Stream[A]) = new Cons(hd, tl)

// Define Fibonacci sequence as a recursive function
def fib: Stream[Long] = {
  def tail(h: Long, n: Long): Stream[Long] =
    Stream.cons(h, tail(n, h + n)) // or h #:: tail(n, h + n)
  tail(0, 1)
}
```

===

### Problem 2

By considering the terms in the Fibonacci sequence whose values do not exceed four million, find the sum of the even-valued terms.

```scala
scala> def fib: Stream[Long] = {
     |   def tail(h: Long, n: Long): Stream[Long] =
     |     Stream.cons(h, tail(n, h + n)) // or h #:: tail(n, h + n)
     |   tail(0, 1)
     | }
fib: Stream[Long]

scala> fib.takeWhile(_ < 4000000).filter(_ % 2 == 0).sum
res0: Long = 4613732
```

---

## Stream Dangers 🔥

[http://blog.dmitryleskov.com/programming/scala/stream-hygiene-i-avoiding-memory-leaks/](http://blog.dmitryleskov.com/programming/scala/stream-hygiene-i-avoiding-memory-leaks/)

1. Define streams using def and never store them in vals
2. Consume streams in tail-recursive functions
3. Pass streams around via by-name parameters
4. Do not pattern match against streams outside the consuming functions
5. Only call the eagerly evaluated Stream methods that are marked as "optimized for GC"

===

### Don't hold onto the head of a Stream 💆

===

Bad `sum` implementation ❌

```scala
def sum(xs: Stream[Int]): Int = {
  var scan = xs
  var res = 0
  while (!scan.isEmpty) {
    res += scan.head
    scan = scan.tail
  }
  res
}
```

A reference to the head is maintained (`xs`)

```scala
scala> println(sum(ones take 100000000))
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

===

Good `sum` implementation ✔️

```scala
def sum(xs: Stream[Int]): Int = {
  @tailrec
  def loop(acc: Int, xs: Stream[Int]): Int =
    if (xs.isEmpty) acc else loop(acc + xs.head, xs.tail)
  loop(0, xs)
}
```

===

### Non GC-optimised methods 🚯

The abstract implementation on `TraversableOnce` is not GC-safe:

```scala
//TraversableOnce.scala
def sum[B >: A](implicit num: Numeric[B]): B = 
  foldLeft(num.zero)(num.plus)

def foldLeft[B](z: B)(op: (B, A) => B): B = {
  var result = z
  this foreach (x => result = op(result, x))
  result
}
```

===

### GC-optimised methods 🚮

However, the overloaded `foldLeft` method on `Stream` is GC-optimised:

```scala
 /** Stream specialization of foldLeft which allows GC
  *  to collect along the way.
  */
 @tailrec
 override final def foldLeft[B](z: B)(op: (B, A) => B): B = {
   if (this.isEmpty) z
   else tail.foldLeft(op(z, head))(op)
 }
 ```

---

## Further Reading 📖

Using *Scalaz EphemeralStream* to avoid Stream dangers: [http://blog.dmitryleskov.com/programming/scala/stream-hygiene-iii-scalaz-ephemeralstream-fills-quite-a-canyon/](http://blog.dmitryleskov.com/programming/scala/stream-hygiene-iii-scalaz-ephemeralstream-fills-quite-a-canyon/)

	</textarea>
</section>
