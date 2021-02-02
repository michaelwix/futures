# Common Use Cases of Scala Futures

## Overview

This document contains common use cases of using Scala Futures. 

## Introduction

`Futures` provide a simple way to run an asynchronous computation. Future starts a computation when you create it and then eventually returns the result. For example, every RPC invocation at Wix is a function that returns a `Future` of the RPC service result.

`Futures` are composable. We consider the following common cases of `Futures` composition:

- Sequential composition with or without error accumulation;
- Concurrent composition with or without error accumulation;

See the details below.

## Sequential Processing without Error Accumulation
Suppose there are functions `fad`, `fbc`, and `fcd`:
```
def fab(a: A): Future[B] = ???
def fbc(b: B): Future[C] = ???
def fcd(c: C): Future[D] = ???
```

We are writing a new function foo: `A => Future[D]` to invoke `fad`, `fbc`, and `fcd` sequentially. This function `foo` _fails fast_, i.e. returns the first failed future returned by one of `fad`, `fbc`, and `fcd`.

## Sequential Processing with Error Accumulation

Suppose there are functions `fad`, `fbd`, and `fcd`:
```
def fad(a: A): Future[D] = ???
def fbd(b: B): Future[D] = ???
def fcd(c: C): Future[D] = ???
```

We are writing a new function `foo: (A, B, C) => Future[D]` to invoke `fad`, and then if it fails invoke `fbd`, and then if it fails invoke `fcd`. This function `foo` does not _fail fast_ and returns a failure only if all of `fad`, `fbd`, and `fcd` fail.

Example: invocation with fallbacks. 

## Concurrent Processing without Error Accumulation

Suppose there are functions `fad`, `fbc`, `fcd` and `case class BCD(b: B, c: C, d: D)`
```
def fab(a: A): Future[B] = ???
def fac(a: A): Future[C] = ???
def fad(a: A): Future[D] = ???
```

We are writing a new function `foo: A => Future[BCD]` to invoke `fad`, `fbc`, and `fcd` concurrently. The composite function `foo` _fails fast_, i.e. `foo` returns the first failure returned by one of `fab`, `fac`, and `fad`. 

## Concurrent Processing with Errors Accumulation

The same as above but `foo` return accumulated failures of all `fad`, `fbd`, and `fcd`.

## Concurrent List Processing without Error Accumulation

Suppose there is function `fab: A => Future[B]` and `List[A]`. 
We are writing a new function `foo: List[A] => Future[List[B]]` to invoke `fab` concurrently for each element of `List[A]`. The composite function `foo` _fails fast_, i.e. `foo` returns the first failure returned by one of `fab` invocations.

## Concurrent List Processing with Error Accumulation

The same as above but `foo` return accumulated failures of all `fab` invocations


