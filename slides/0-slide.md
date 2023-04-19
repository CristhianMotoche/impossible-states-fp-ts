<!-- classes: title -->

# Making impossible states with TypeScript and fp-ts in a React Application

<!-- block-start: grid -->
<!-- account: twitter, camm_v222 -->
<!-- account: github, CristhianMotoche -->
<!-- block-end -->

---

# Who am I?

Cristhian Motoche


Software Developer @ Stack Builders


Haskell, Elm, TypeScript, Python, etc.

---

## TypeScript

```ts
// Type alias
type UUID = string

// Unions
type Role = "admin" | "author" | "reader"

// Literal types
type Comic = {
  title: string
}

// Interfaces
interface User {
  name: string
  role: Role
}

// Types from values, type indexing, etc...
```

---

## TypeScript

```ts
// Type Variables!
type Either<L, R> = Left<L> | Right<R>

interface Left<T> {
  left: T
  tag: 'Left'
}

interface Right<R> {
  right: R
  tag: 'Right'
}
```

---

## fp-ts

```ts
type Option<A> = None | Some<A>

export type NonEmptyArray<A> = Array<A> & {
  0: A
}

type Either<E, A> = Left<E> | Right<A>

// Async Operations that never fail
interface Task<A> {
  (): Promise<A>
}

// Async Operations that may fail
interface TaskEither<E, A> extends Task<Either<E, A>> {}
```
---

## fp-ts

It provided some useful helpers:

```ts
// pipe to apply reversed function application
pipe(
   users,
   findFirst((x) => x.role === 'author'),
   fold(
      () => <p>{'Cannot write in here'}<p>,
      (x) => <p>{`${x.name} is an author`}<p>,
   ),
)
```
---

## fp-ts

Also, type classes along with type instances!

```ts
// Type class represented by an interface
interface Eq<T> {
  equals: (a: T, b: T) => boolean;
}

// Type instances
const userEq = Eq<User> = {
  equals: (a, b) => a.name === b.name && a.role === b.role;
}

// Ord, Semigroup, Monoid, Functor, Applicative, Alternative, Monad, Bifunctor, etc...
```


---

## Making impossible states impossible

[Making impossible states impossible - Elm](https://www.youtube.com/watch?v=IcgmSRJHu_8)

By Richard Feldman

![](./making-impossible-states.png)


---

## Making impossible states impossible

The purpose is to:

> Get compile errors to avoid undesired states

---

## Bye👋
