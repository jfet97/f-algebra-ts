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
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128#code/PTAEAcEtwUwKAGYFcB2BjALpA9ii0YAeNXAZw1AEEA+ACgEMAuKgSmcoG5FVMc8pYxMhUoAaUACE6TKuPoAjZg3YtQAXmqS2krsnRZc+QSRTlZk8QGFp7OYtDLW6zRPHy0S+xNUbQl7Za6PAb8BEKmIuKufuIAInRwAJAyYkkKSik+LqJJ7p7M3s4xSWgAJkoeflmgsTnasUH6fEZEJmZiFjE14gCiCcm2afaOlNWuuZW0XtWWOYllFcz+RbVwoKClMErlNdU9dcw9jbyGAjC0acyoANYo2ADuKHMKAPzMAGLBfHPub6CfTVwczKfwBJyeaw2MFBX1wcG0Nzuj1AAG8kqR7pAMGgABYOegAJwA5kgALYwFAYUgAOgANhSiRgcao0Yl5vRSDBQABGRhJNkEmAYJAEvD0LhstAcrkAJj5bMSguFotACgAhAwWBL2ZzQABmeUCoUivDuDXqzVa-lS3UAFkNiuNKrKGrNDHk5pYXq463WNq5AFYHUqTVCNS6pmhzR7Ld7+ZsEPQkLSMMxWWz6RQlepVcSyRSqQBtAAMAF1tYkENgCQ5M6BIDnuRx66BCLmSeTKTT6ShGTjm5AANSDln8o0UNTt-NdwuQUu0JVWhUAXzHIZVSu1q8Sq9XIFAAFoj8eT6ez+e4HBIJSYATE2guQAJADSABVUZCAPqUK4oW4PFAfVADAAE9YF-f9Hi4Vc4FA2BQGfa9SkId5QBgAAPDAKVKUhQBfV9xBoHNaFQgAyVFQG-dhQGXFhCwAIjgmB6PLS99yYhCkJQtDMOw3D8N6QjNEnVCMKwlAcI-fd1kFehSlwWkQOAsCtlAREALgfdl1AF4HDIqSwBkmA5IUpTPx6Q5NMM0BZPklBFMon8qCsmi6MYlSWJc9Y0y831bNM-4Pl8oyTPssyLIcPZgps4y7IcqjIqKShfJg68sLvegH3+YJq24sS+Lwt9NDREK4qU0l6HAZhCA6KRaE5WkEGYRCJJQoTxCa-EVCKQpIXWXwWuQ94omoOAYP3BBIHQy80FpDlcPeKa8t4iT+KKj8-WEAkkEwataHAJB5FpSA0Bi0KHIAN3oWkkFUwa2v+Jb3moagWRosbLz0cFQEm9DlvEyT8LoK6brurjhsev7ntej4nuKyF1zwFAYHuSHaBB26rRgr6QjUlBfv+gqgdodLSVhqGYc41qIcWin4aM5U8FJ6kMZgaDLw4yhaSJGB5AJehCdWwqCKoYSHETZrwaE6pOE+2E8ClDABdElbAbfITaE6sEMFy565G59hud5-mHpobRaHQ8mUNepKNr8p08At6prqJTXqQq8BaFQX6nfERX6E1-WiS9FgPpc47yFIS80tve8uQAOUgWlUJKs6ysopWiWYAByRPk+zsP9x6etcKZLkOOwBBgJxLlWdw69q65COMBcygS8b0ApQJAlIFvZTYCvG8MqyywyHeQh9lFu3-LCjP6Cz0Bs9H0x3gL0qAtZyz19nrue9vdgw7aCgUCTzraGqPPUMnc+iloFE54X3Ok9X2i4CPzuyE6ieNdUyfMiKZepBx77CItfGAoh6DVDvpCTeaEch+kJHvAkzB6DwIfsweigD3j0TGqHWCKlQAABlIDkGAUJHMl9QAAB8-Bj2-qLaOQ845EJIRgd4PRPz4QnpofKQt8LT1igFJizBiGkPoUyEhDFvwsWoOzN+whQDNwQKpbhN9fDa1yqIthHCuF9DFmozQ0D1geyUBVDqUCKrUk-JndQahJxP3zjpUAFVQDMDaAgWgljWYdU8fQaku9e4Ei9KAUOr98HwS0dwnMtNCBaPYZwt83DRryIiKAE+tIlDVEicjC6t59HVB9ukjxXoUlmDaNVPotBf7iAySw8g3CsmsKiWA0QtIClTVoO4qpNSSnh1YaqI2fMORsTABVa4XJ6CKP6S7Y29BSkUC0VzHmQyKmiBoLQXAl9MlJXELgLBSgth-0YKMJKMtBkmziToxJfRyHX02c-XZKAsFQObu8aoqdIAeNeVYmxdjQD0UvvRVQiNQD3OTufSEMBaS6hBXssetBvneKmaQ-xiDAmhJGaAEBoBMS0lpKAeQXJrzkHoJSSA9AYClFVLhUg2BDAcjqRgJZsz25SjxZS8QtKcVchxPQXJwFsAQGwOQcAuBy410gDWYlSsyX0BCC5JApBrxElVIo+gpJ5ClEmTrUARI5U1xrGXGykAiQ4goCYNASpy4ELrqqNAJACSlGVQKnFBqJlinOZMkhalOSlExdnSg2dQVV2znErFVAg3ep6IWUs8yqDd3oCBZlQycyqMaeQZNJt9jRtLAkdYBjQAxvEPuMF7xITo2urdP2aLbzVELEi6kjaAm3lLKAEtTyx7wn9YG4Ni8w3Fx7d6lAZJCUEl6M4xVFBeGSWHRq28caADKZJM2TMnBPHiANcKztHTmbdeSC2LM9fQvdBI82JV8MWYtYBS3lqRc2oJRRWagEHJ3GtNZ21YK7S5bOAag2Vz7aw1CA7I1bpHbecdpJJ0boKieuNAAFAk2BSgrtTcXadoG501knCeug6amVHsnjhi4+bqjcivaClAl9b2VvAa+7u6LH00dAAAKjo0gtt16O0ry-fuX9vbQ2AfDYOmlGAe69kXaJ5VKG12VLwyu+h5AxNEjPQW+i6T6LkZvfmu9b7qgLsk72CtoNVAvvoowRg9Fn1scCRxijn68H7nAIhgAVjAb65nw0Hk0FfTQfH-0CdIUJkDAGzCr3gy5tzIRpOtkns9faEXwQHMYD0aobyiiHuWVm8QkTobEbSUnTTlHn7UdBtW+jtaihtHi9gVz4IjOYzK0g1QH7O0ObAH5kN-aI3twCxQHoBd34IcpSdOVMBouqKc8NxWqkqnJeqPIOl9JSVyYI9lppei8vpMK1R7TNHGsMd8JNp1036swFULpKrOnysPrcW+2zcLuNtZ4uq8A9Io5xubgAeQQPHMDBJSAiKaThnMVWAx+zILQW04PTC0D1ND0gtAZTw9oGR-LtJz4h16fuSgCak2euAjAcgcAzh5a+z9v7pA0H+wRaw5R59CK45XRjtBtBTDVDaNgekdJsCuzZzxsAS7SQoawkTknkIye-cw5TyE1OlHnBYOIQX8miNehZygdnZBOcwG57z0OT2ENIeF4TluYv1gS4p1TuVAc5f09AAb5DnrmflvV5VzXXPaQ89Z3rzF+mlNG9FwQUnrDvuS9HdLhBSsafkDpwr0AvupOO9V+W0gGvTBa51-Vb3LkDe1ai-jkXJvA-i+D+TqXlvI829jznyLfAmfoSKPX1j3JixJ-zWzoophy-W9p-Lhn-M8eZYDq3hw7ffAc-d57vn+uEt58HwTgPsAg-kBDxbmXVuo8YBj+Iav4Imfx97MP+qKeiikHD6+ivPfbc4-70zw-p-U+0on67e--O7cz9r-n43xOi9m5L6H28Z+sul+Vegox2o2dePUi2xkKAFsoAAApKADKCHGrsfr4J3mvhftHr3vGjfongrs7qgZoOPtrh7rzinvCEAA)
