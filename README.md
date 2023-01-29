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
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128#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-u4iXHcOknw-R7xXGyOgAAAoEmwKUaj17R6EZ01h-ek4uN0FveQajnGHPcYuK+6o3IBO6pQNA4T-6i52rE74CTAAqaTzDZMBfg3w-cKnkPbo07u9DQaMC717CZ8jWWq3Wdo0C1zGB3MtXINlokPG30cTuRxfzQnX0ibC9UPLlW-1I1UFJjijBGAcUkzF-esGEs4vABZgAVjAemvXd0Hk0DAzQyW1OpdEel7T6mzCvxM+Z7Ak3wSFdbC1bGF0JtTZCByxgPRqhyseXejjLUTnYx43chrgWinBaRqFoD9qihtBO7ts7fAOvUy+801Qw2FOJbAEtrdqG92bxWxQHoQ8MHmf9bdUtMADs9LG+j0271gWXeqPIEN9II4lbK+IaFQNvOgBe3FxrDhmvffC5oXHk78fA5gKoEyf3mdg-KYNmsEOX6Kf3HhOt4B6Q1xM6vAA8ggRunnSDMCeZ50Zf2AxFzILQW02vTC0D1Pr0gtAZTG9oH5unLdIFl1RfuU9ysL0Lp0jAcgcAzi0-l4r5XbDi5dMgRRM9TvqWhy9Gw2gphqhtGwPSOk2AM6R7F2ASjpJrMETdx7yEXulckYJKQX3pbQ7+8LqAFP7nnM2-DygKPZAY8wDjwn8uUOzMWas879Pa9M-rGzz7yEfvVEIHOCXnbbeQ+V9-dX37tfY+0njxHpvOK2sFfb67zvBBPeqIVzn7D+e++F4tQPof4gl+9mo+P19pAa+mDrw39qC+vI7b2yENPq-3fr6z5v73ufd+MLNgf8gg+AeLeAO+2C6PsRQuEoA0W3IxYYeE+l+RQpgBef+xegejuZ+cBr6keU+1+M+c+iezej+gOuAL+Ge7+3en+2+t4P+28KBh+QBRBoBY+J+RImBDgpACBvgHByBRe9BJeDu56GBhcv6HBV+waeBGcohSewBT+fApBa+sAG+5AW+vev+vBABR+LeeOmOZ+kBvgEgpOVkKA4BAApKADKGXFXpwZoEgXvnQRoUAQIcHpGufg4Ngb4NHhIRHpfvCEAA)
