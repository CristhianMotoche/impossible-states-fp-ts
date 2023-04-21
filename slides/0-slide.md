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
const userEq: Eq<User> = {
  equals: (a, b) => a.name === b.name && a.role === b.role;
}

// Ord, Semigroup, Monoid, Functor, Applicative, Alternative,
// Monad, Bifunctor, etc...
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

For example, this interface:

```ts
type Role = 'author' | 'reader'
type Comic = { title: string }

interface User {
  name: string
  last_name: Optional<string>
  roles: Array<Role>
  favoriteComics: Array<Comic>
}
```

allows for this type of scenarios:

```ts
const reader: User = { name: 'Cristhian'
, last_name: O.none
, roles: []
, favoriteComics: [{ title: 'Gremory Land' }]
// ^^ User without role but with last access!
}
```
---

## Making impossible states impossible

Let's avoid that creating types to avoid those representations:

```ts
interface User {
  name: str
  last_name: Option<Date>
  roles: NonEmptyArray<Role>
}

interface Author extends User {
  roles: ["author"]
}

interface Reader extends User {
  roles: ["reader"]
  favoriteComics: Array<Comic>
}
```

---

It will complain if we try to define something like this:

```ts
const oddReader: Author = {
  name: 'Cristhian',
  last_name: O.none,
  roles: ['author'],
  // ^^ Invalid type & Missing keys: favoriteComics!
}

const oddReader: Reader = {
  name: 'Cristhian',
  last_name: O.none,
  roles: ['author'],
  // ^^ Invalid type & Missing keys: favoriteComics!
}
```

---

## Thanks
