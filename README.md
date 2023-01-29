# F-algebras in TypeScript

(Thanks Wikipedia)

In mathematics, specifically in category theory, F-algebras generalize the notion of algebraic structure. If __C__ is a category, and _F_: __C__ → __C__ is an endofunctor of __C__, then an _F_-__algebra__ is a tuple (_A_, α), where _A_ is an object of __C__ and α is a __C__-morphism _F_(_A_) → _A_. The object _A_ is called the _carrier_ of the algebra. When it is permissible from context, algebras are often referred to by their carrier only instead of the tuple.

F-algebras constitute a category, and the initial object of this category, namely the __inital algebra__, is the one with an unique morphism to all other algebras in the category. In other words we can evaluated the initial algebra to whichever carrier type we want. \
Various finite data structures used in programming, such as lists and trees, can be obtained as initial algebras of specific endofunctors.

###  e.g.

Let's define the following non recursive data type:
```ts
type ListF<E, A> = NilF | ConsF<E, A>
```
This type could be easily made a functor on `A`, that is the carrier type, whereas `E` is the type of the element contained in this weird list.

How does an algebra look like? If we choose `number` for both `A` and `E` we can create an algebra that sums the values:
```ts
const SumAlgebra = (listF: ListF<number, number>) => {
  if(listF is NilF) return 0
  else return listF.A + listF.E
}
```
This algebra has the type `ListF<number, number> => number`.


### Fix

Using the encoding of Higher Kinded Types, made available by the [fp-ts](https://github.com/gcanti/fp-ts) library, is possible to define the `Fix` type constructor, which kind is `(* -> *) -> *`, to then derive the least fixex point of an endofunctor. This fixed point is the initial algebra we are looking for.

Applying `Fix` to `ListF` we get its fixed point:
```hs
type List<E> = Fix2<ListF<E>>
```
Now we are able to create list of whichever length we want to, instead of being limited to only one value. Now let's say we have a `List<number>` and we want to sum all the numbers into it. How would you do it? You could write a recursive function of course, but you don't know yet that the `SumAlgebra` defined above is all you need.

It couls sound strange at first, because the `SumAlgebra` is able to handle only one step.\
The main point, that follows from the properties of F-algebras and their relation with the corresponding initial algebra, is that we can write one function, only one function, that is able to work with all the possible fixed points (data structures) and all the possible algebras (evaluation strategies), which given a fixed point and an algebra will evaluate the former, that could be arbitrarly deep, using the latter.

This magic function is called __catamorphism__, _cata_ for friends.

### A random CT diagram

![https://i.imgur.com/MWFJyjA.jpeg](https://i.imgur.com/MWFJyjA.jpeg)

### Some great videos on this topic

* [Programming with Categories - Lecture 9](https://www.youtube.com/watch?v=YhbBvSgO9xU&list=PLhgq-BqyZ7i7MTGhUROZy3BOICnVixETS&index=12)
* [Programming with Categories - Lecture 10](https://www.youtube.com/watch?v=0Q3NDZ6yxz0&list=PLhgq-BqyZ7i7MTGhUROZy3BOICnVixETS&index=14)
* [Programming with Categories - Lecture 11](https://www.youtube.com/watch?v=jpl7FE2TZTE&list=PLhgq-BqyZ7i7MTGhUROZy3BOICnVixETS&index=15)

### The purpose of this repo

It is only a proof of concept. It seems we can do this in TypeScript, but should we?




## How to run


```sh
npm i

npx ts-node algebra/index.ts 
```

## A playground
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128#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-rhyzgubP1gc-RzzjlCs+egDl7B4nSbRcZfF1T2kNPScy-qXJvlzOEdK4ICj0h13OcNox3-bHWvLcceNXrqNpAxemAl1Lgy5uL1uY8yEa3rO7fs4d2jrnLu-Qa5xxgPH2vsuea97hIomeiPcmLML-XvuiimHV270hKfoc+t1-nqNZPDf++N6bunsu0-h4VzbpHUeVcx6d7eePWPNdl-EKHnLuBdce-tlbJNpBC++GnyXgfuP8cw6r5Pn3M-NAU4b4bafwf9zD-T3O+HkfYD2-II7tXruF-J6H4KZbLXdeZ98BIQbdCUA61AAAUlADKB2Iv19Lz7150HyoAJxXz-z91RS31J193hCAA)
