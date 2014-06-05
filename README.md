Swiftz
======

Swiftz is a Swift library for functional programming.

It defines purely functional data structures and functions.

Examples
--------

**Data abstractions:**

```swift
let xs = [1, 2, 0, 3, 4]

// we can use the Min semigroup to find the minimal element in xs
sconcat(Min(), 2, xs) // 0

// we can use the Sum monoid to find the sum of xs
mconcat(Sum<Int8, NInt8>(i: { return nint8 }), xs) // 10
```

**JSON:**

```swift
let js: NSData = ("[1,\"foo\"]").dataUsingEncoding(NSUTF8StringEncoding, allowLossyConversion: false)
let lhs: JSValue = JSValue.decode(js)
let rhs: JSValue = JSValue.JSArray([JSValue.JSNumber(1), JSValue.JSString("foo")])
XCTAssert(lhs == rhs)
XCTAssert(rhs.encode() == js)
```

**Concurrency:**

```swift
// we can delay computations with futures
let x: Future<Int> = Future(exec: gcdExecutionContext, {
  sleep(1)
  return 4
})
x.result() == x.result() // true, returns in 1 second

// Channels
let chan: Chan<Int> = Chan()
chan.write(1)
chan.write(2) // this could happen asynchronously
let x1 = chan.read()
let x2 = chan.read()
println((x1, x2)) // 1, 2

// we can map and flatMap over futures
x.map({ $0.description }).result() // "4", returns instantly
x.flatMap({ (x: Int) -> Future<Int> in
  return Future(exec: gcdExecutionContext, { sleep(1); return x + 1 })
}).result() // sleeps another second, then returns 5
```

Implementation
--------------

**Implemented:**

- `Future<A>` and `Chan<A>` concurrency abstractions
- `JSON` types and encode / decode protocols
- `Semigroup<A>` and `Monoid<A>` with some instances
- `Num` protocol
- `flatMap` and `maybe` for `Optional<A>`
- `Dictionary` and `Array` extensions

**Typechecks but currently impossible:**

- `Either<A, B>` with `Equatable`
- `List<A>`

**Note:**

The "currently impossible" data structures we think the language intends to support.

**Not realised:**

These abstractions require language features that Swift does not support yet.

- `Either<A, B>` crashes with `unimplemented IRGen feature! non-fixed multi-payload enum layout`. `rdar://17109392`
- `List<A>` by an enum crashes the compiler. `rdar://???`
- `List<A>` via a super class and 2 sub classes crashes with `unimplemented IRGen feature! non-fixed class layout`. `rdar://17109323`
- Functor, Applicative, Monad, Comonad. To enable these, a higher kind,
  C++ template-template, or Scala-like kind system is needed. `rdar://???`

**General notes:**

- `enum` should derive Equatable and Comparable if possible, similar to case classes in Scala. Or a deriving mechanic
  like generics should be present. `rdar://???`
