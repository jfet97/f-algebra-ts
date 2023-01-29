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
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128&ssl=65&ssc=7&pln=55&pc=1#code/PTAEAcEtwUwKAGYFcB2BjALpA9ii0YAeNXAZw1AEEA+ACgEMAuKgSmcoG5FVMc8pYxMhUoAaUACE6TKuPoAjZg3YtQAXmqS2krsnRZc+QSRTlZk8QGFp7OYtDLW6zRPHy0S+xNUbQl7Za6PAb8BEKmIuKufuIAInRwAJAyYkkKSik+LqJJ7p7M3s4xSWgAJkoeflmgsTnasUH6fEZEJmZiFjE14gCiCcm2afaOlNWuuZW0XtWWOYllFcz+RbVwoKClMErlNdU9dcw9jbyGAjC0acyoANYo2ADuKHMKAPzMAGLBfHPub6CfTVwczKfwBJyeaw2MFBX1wcG0Nzuj1AAG8kqR7pAMGgABYOegAJwA5kgALYwFAYUgAOgANhSiRgcao0Yl5vRSDBQABGRhJNkEmAYJAEvD0LhstAcrkAJj5bMSguFotACgAhAwWBL2ZzQABmeUCoUivDuDXqzVa-lS3UAFkNiuNKrKGrNDHk5pYXq463WNq5AFYHUqTVCNS6pmhzR7Ld7+ZsEPQkLSMMxWWz6RQlepVcSyRSqQBtAAMAF1tYkENgCQ5M6BIDnuRx66BCLmSeTKTT6ShGTjm5AANSDln8o0UNTt-NdwuQUu0JVWhUAXzHIZVSu1q8Sq9XIFAAFoj8eT6ez+e4HBIJSYATE2guQAJADSABVUZCAPqUK4oW4PFAfVAT8el-f9HiAjAAE9YDApFALgVc4Gg2BQGfa9SkId5QBgAAPDAKVKUhQBfV9enEGgc1obCADJUWAn9zBAw5QGXFhCwAIhQmAOPLS9924tCMKwnD8MI4jSPIqhNEnbC8IIlAiI-fd1kFehSlwWkoNAbi4IAuB92XUAXgcWjlLAVSYHUzTtOY0AegMizQDUjSUC0hj2Ec1j2K4mCeNLLz1jTQLfRcmz-g+ELLOstzbNAhw9ii5yrNc9zvyUapKBCpDrwIu96Aff5gmrET5PEki300NFotS7TSXocBmEIfZzCkWhOVpBBmHQxSsKkmhxC6-EVCKQpIXWXwesw94pKkRD+LABBIFwy80FpDliPeZbSrExSJMqj8-WEAkkEwataHAJB5FpSA0GSmL3IAN3oWkkC2ITepm0BEQA8QttwrDqGoFlWPm7hATwJaAbk3alNIuhnte96pr676-3gv7tveIHtH+wHDuS5U8BQGB7n+ZbaERt6rSQvRwTRqGdoUuHKtoPLSQ+LHge64Svp+x5MehoGCfXPB2epKmYC4JDBMoWkiRgeQCXoJnysk6SqMTHnPvEfmnmkzKuHB+mpQwFWYeZ-ayOk2ghrBDASuxuR5fYeXFeV1GaG0WhcM5oXMuNkJQFN+hbeYegUCg52iXDyPR19QnQx92OoOqF6iVt6l6vAWhUChn2WHEEPbejr0WDBumg5DmVVb2irrZoZPyaFv6PmKglAej12FaV82KO5qhA+aavaFAX2I6j0AhonrvVTjgnLKJ1V5cz7Pc5QfPcML4P6DNkvl6JMuwa8m7yFIS9ctve8uQAOUgWlsOq+7auAs2Y9AAByO+H4-sH9x6esxEmRckEtgBAOkcRcklsRa8ECuSnwwF5SggC4E7wJASSAt4dJ+SvDefKhVLBkHeM1fuIsUrhU-G-ZgH9CGmHeL-Gq4VJaHEhGFWKaCMG3k8khNoFAUD3yGrQao39sKTiEUUWgKJX70Hfl-e+9C2JwF4cHMgQ0SE23ei1TIRRaGkGIfsSiYiYCiHoNUSRkJmE4RyH6QknCCTh2sdI9+HFdHvA4ohcuyE-KgAADKQHIPo0hk4RGgAAD5+CIeomgF88HX18f4jA7xSKiUtvXMhD1tK6XiQEwgTJ-GcRArxcQeTSAFMoLxag0sYl5TiX4gJPRSLNU0GVOuySn5sPclkupiT1ElLKRUqpSjhCgAQXbduhBulJIOpOJ+2clD1UGmY+q1JKEyPUGoSccif7GVAPVUAzA2gIFoMsyWg1jn0GpFKdBmCCRelAOXHhwzRnvSaeI3w9sSqTIaW+JpMkEoSNmQ1eZ9BFkSOWasok6zNkiI-jsvZBzVHnIli9N6ZzllXLsXch5njBLdKaTmPGXzGl9GoKtYZ-DaQZSKHikmj1bx-LeZofOFKjleiGREFRpgmp9FoJo8QlLskYCadUPFfQqLGNpNUfOhzeX8rZSfBJB93YcgWrs+g1wuT0BGYq9Oyr2VmG6XLHuytuWiEbrgERVLfAdFwK4pQWwtGMFGEUZ11q3a9wmQk943zXxNKCQ4C18jxC2qIWYhB7xqhP0gEc8NKy37rNABxERHFVCi1AIGh+QjIQwFpLqNNIa6G0Fjac7VATLm2JuQ81VBjQCYlpLSUA8guTXnIBHLA9AYClFVMRUg2BDAckFUa5VKCpT1s7eIXttauQ4noHSnS2AIDYHIOAXAIDIGQBrC2s2lJIC7z4F5JApBryQq1etUk8hShaodqAIku9IE1mAc5SARIcQUBMGgJUIDvHQNVGgEgBJSjHvnbW+9mqxTuuVigw9nbVUf0oLCsBn9Jn2SoLC-x9lCwBWUZQdB9AoJDt7jmV5IqEkEY9vsHomGEjrEZaATD4h9wZveJCSmKLjEcMrUUQsJbqS8YxTc0soBGMoFcfCWD8H03gI-shgBEn0MoDJE2gkvRdmHooC0pSCnz23n1RQAAymSMjWrJzNRSeVLTSmcwWfpbRw1EGVYtWswSaj-zfDFgY2AJjLGS38dvNUSWoBBwcawcJ0Tnj9wfzgwhqTMnUMoKcyp0kamzN1yc7p0AAAFAk2BShGaIwAjTxEnNWcUzZkj5AjPqKcy52j3IPPppQCI7zbGi4Vr80UALAAqYLNZQtELE15KLkmkNepQ3JntGAMG9nS3pybx68smZ5eVjAlWWrkCm0SGr1QOIUo4vVrzNGfNtduUUWbG3WNI1UEFjijBGAcUCz1oTnmRP9fC2AcA2WABWMB6a3ZQweTQojNBDcQ9J0bsm0PETB2Yeh6WsvYG++CBbrYWrYwul9n7IR7WMB6NUCN1LSP2fUXi7GLmKX7ca-I5rSNWvXPa74No6OEeY74Bd6mtPMVPYa2F8T0WRsBLG5D-nFAei-2UVlztt1d4wGR68j7kvTbvV5Tj6o8g+30gjst1b4hRVAwuOscnXODsOCO3Tk7vh5eAcV2zmAqgTKM9N5zg5x2jcvboQN-ceF6CknAPSc+6WEEAHkEA31KwSUgzAaVh7+YzgMRcyC0FtPH0wtA9TJ9ILQGU6faB1dABSoRZd5X7hw8rfD9mdIwHIHAM4+vS0YGD6H7T4fHHF2eUIiiuGy-GtDl6RxtBTDVDaNgekdJsAZwHx7sABnSR5YIlXmvkIg8h7D6QFvu9Q5t+3tPyr1WC995QIPsgw+YCj-H+XN7mXsu5fL3PxBC-1hL8b0p1fkJW8JIQOcbe8Pr-d73yxg-RQQ+I+tIY+-e5+qqZ282N+led+BAtej+K+a+e8m+4gkBvYRmf+NGpAh+pgx+p+7U4BXk8OiOIQs+MB1ecBi+CSDeiBr+6+Ra7+n+4gxBLOuAGBuERQHB3W3IxYve-+2BRQpgSBG+jB7eVAneGBfBNGA+gBR+wBoBE+F+LBSO0B8+lBD+1By+TeL+NiyBohX+GOKhv+aBh8hcLGpAAhvgFhwhDB5AH+YhJeeGkhZhWBlhmgQBJ+IBGcFhhB+4yhpBqhsBsA8BmhT+t4OhO8ehdhTBl+Cu0u7Bo06uVkKAPsoAAApKADKGXPvm4XnhEW-tEQ4RIfZpgQ4DIQznIZ4QodgfCEAA)
