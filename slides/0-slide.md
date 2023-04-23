<!-- classes: title -->

# Making impossible states with TypeScript and fp-ts in a React Application

---

# Who am I?

Cristhian Motoche


Software Developer @ Stack Builders

<!-- block-start: grid -->
<!-- account: twitter, camm_v222 -->
<!-- account: github, CristhianMotoche -->
<!-- block-end -->

---

## Things I like from Haskell/Elm:

- Strong and Static Typing

- Functional Programming

---

## TypeScript

```ts
type UUID = string

type Role = "admin" | "author" | "reader"

type Comic = { title: string }

interface User {
  name: string
  role: Role
}

type Either<L, R> = Left<L> | Right<R>

interface Left<T> {
  left: T
  tag: 'Left'
}

interface Right<R> {
  right: R
  tag: 'Right'
}

// Types from values, type indexing, etc...
```

---

## fp-ts - Types!

```ts
type Option<A> = None | Some<A>

type Either<E, A> = Left<E> | Right<A>

export type NonEmptyArray<A> = Array<A> & {
  0: A
}

// Async Operations:
interface Task<A> {
  (): Promise<A>
}

interface TaskEither<E, A> extends Task<Either<E, A>> {}

// Set, Records, Transformers, etc.
```
---

## fp-ts - Helpers!

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

## fp-ts - Type classes and Type instances!

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

For example, a form to order a pizza:

![](./order-pizza.png)

---

```ts
interface PizzaOrder {
  mainSelection: Option<PizzaType>
  secondSelection: Option<PizzaType>
  extras: Array<Extra>
}
```

allows for this type of scenarios:

```ts
const order: PizzaOrder = {
  mainSelection: O.none,
  secondSelection: O.none,
  extras: ['meat'],
}
// ^^ Don't have type but include extras?
```
---

## Making impossible states impossible

What about this?

```ts
interface TypeAndExtras {
  type: PizzaType
  extras: Array<Extra>
}

interface PizzaOrder {
  mainSelection: Option<TypeAndExtras>
  secondSelection: Option<PizzaType>
}
```

It allows:

```ts
const order: PizzaOrder = {
  mainSelection: O.none
  secondSelection: O.some('vegetarian')
}
```

---

## Making impossible states impossible

How about this?

```ts
interface Selection {
  main: PizzaType
  second: Option<PizzaType>
}

type NoMainSelection = {
  tag: 'NoMainSelection'
}

type SelectedMain = {
  selection: Selection
  extras: Array<Extra>
  tag: 'SelectedMain'
}

type PizzaOrder = NoMainSelection | SelectedMain
```

---

## Define functions and type instances over it

```ts

export const noSelection: PizzaOrder = { tag: 'NoMainSelection' }

export const mkSelection: PizzaOrder = (main: PizzaType) => {
  selection: {
    main,
    second: O.none,
  },
  extras: [],
  tag: 'SelectedMain',
}

export const getPrice = (order: PizzaOrder): number => { ... };

export const pizzaOrderEq: Eq<PizzaOrder> {
  equals: (poA: PizzaOrder, poB: PizzaOrder) => { ... }
}
```

---

## Use hooks that work for this types:

Use [fp-ts-react-stable-hooks](https://github.com/mblink/fp-ts-react-stable-hooks):

```ts
// useStable -> useState
// useStableEffect -> useEffect
// useStableMemo -> useMemo
// useStableCallback -> useCallback

const [pizzaOrder, setPizzaOrder] = useStable(PO.noSelection)

useStableEffect(
   () => { ... },
   [pizzaOrder],
   Eq.tuple(pizzaOrderEq),
)
```

---

## Thanks!
