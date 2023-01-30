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
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128#code/PTAEAcEtwUwKAGYFcB2BjALpA9ii0YAeNXAZw1AEEA+ACgEMAuKgSmcoG5FVMc8pYxMhUoAaUACE6TKuPoAjZg3YtQAXmqS2krsnRZc+QSRTlZk8QGFp7OYtDLW6zRPHy0S+xNUbQl7Za6PAb8BEKmIuKufuIAInRwAJAyYkkKSik+LqJJ7p7M3s4xSWgAJkoeflmgsTnasUH6fEZEJmZiFjE14gCiCcm2afaOlNWuuZW0XtWWOYllFcz+RbVwoKClMErlNdU9dcw9jbyGAjC0acyoANYo2ADuKHMKAPzMAGLBfHPub6CfTVwczKfwBJyeaw2MFBX1wcG0Nzuj1AAG8kqR7pAMGgABYOegAJwA5kgALYwFAYUgAOgANhSiRgcao0Yl5vRSDBQABGRhJNkEmAYJAEvD0LhstAcrkAJj5bMSguFotACgAhAwWBL2ZzQABmeUCoUivDuDXqzVa-lS3UAFkNiuNKrKGrNDHk5pYXq463WNq5AFYHUqTVCNS6pmhzR7Ld7+ZsEPQkLSMMxWWz6RQlepVcSyRSqQBtAAMAF1tYkENgCQ5M6BIDnuRx66BCLmSeTKTT6ShGTjm5AANSDln8o0UNTt-NdwuQUu0JVWhUAXzHIZVSu1q8Sq9XIFAAFoj8eT6ez+e4HBIJSYATE2guQAJADSABVUZCAPqUK4oW4PFAfVADAAE9YF-f9Hi4Vc4FA2BQGfa9SkId5QBgAAPDAKVKUhQBfV9xBoHNaFQgAyVFQG-dhQGXFhCwAIjgmB6PLS99yYhCkJQtDMOw3D8N6QjNEnVCMKwlAcI-fd1kFehSlwWkQOAsCtlAREALgfdl1AF4HDIqSwBkmA5IUpTPx6Q5NMM0BZPklBFMon8qCsmi6MYlSWJc9Y0y831bNM-4Pl8oyTPssyLIcPZgps4y7IcqjIqKShfJg68sLvegH3+YJq24sS+Lwt9NDREK4qU0l6HAZhCA6KRaE5WkEGYRCJJQoTxCa-EVCKQpIXWXwWuQ94omoOAYP3BBIHQy80FpDlcPeKa8t4iT+KKj8-WEAkkEwataHAJB5FpSA0Bi0KHIAN3oWkkFUwa2v+Jb3moagWRosbLz0cFQEm9DlvEyT8LoK6brurjhsev7ntej4nuKyF1zwFAYHuSHaBB26rRgr6QjUlBfv+gqgdodLSVhqGYc41qIcWin4aM5U8FJ6kMZgaDLw4yhaSJGB5AJehCdWwqCKoYSHETZrwaE6pOE+2E8ClDABdElbAbfITaE6sEMFy565G59hud5-mHpobRaHQ8mUNepLuEBBX6CVzXmHoFAQP1okXbd0dfRixmHEt1VvaKa6iU16kKvAWhUF+i2WHERX6E1j2vRYD6XOO8hSEvNLb3vLkADlIFpVCSrOsrKKVz3QAAciLkua-T-cenrXCmS5DjsAQYCcS5VncOvHuuUzjAXMoVuh9AKUCQJSBb2U2ArxvDKsssMh3kIfZRY28uAs-KvmBrtfTHeRvSoC1nLPPsKp8JWfb3YdO2goFBi862hqnr1DJw-opaBRSu9Bq512LqfWicBn5TzIJ1TeGtVJb0yEUY+pAN77CIj-GAoh6DVH-pCS+aEch+jvnPAkLtCGAOrvRZB7x6JjTTrBFSoAAAykByCoKEjmL+oAAA+fh16wNFjnZe+dmGsIwO8Hon58Kb00PlIW+Ed7+RvkxZgLC2ECKZKwhi34WLUHZhA4QoAR4IFUjI3+vhta5TUeIyR0i+hi3MZoXB6xI5KAqh1HBFVqT7yAeoNQk4QENx0qACqoBmBtAQLQLxrMOpRPoNSae98CRelAGncBDD4LWJkTmWmhBrESKkW+GRo0DERFAK-WkShqhZORhdW8DjqixwqZEr0pSzBtGqn0Wg8DxCVNEeQGR1SxHZIwaIWkjSpq0Aid03prSM5iNVEbPmHI2JgAqtcLk9AjELNDsbegbSKDWK5jzZZnTRA0FoLgL+VSkriFwNQpQWwEGMFGElGWSyTb5NsUUvoHCf5XNAXclA1CcEj3eNUMukBIlgu8VXPxoB6Jf3oqoRGoAAUlw-pCGAtJdSovuevWgMKYnbLYQk4ht40mrNAGg0AmJaS0lAPILk15yCuywPQGApRVS4VINgQwHJ+kYGOXsieUp6WcvELy2lXIcT0DqcBbAEBsDkHALgDuvdIA1hZUrSkkBHZ8BckgUg14iSqiMfQUk8hShbJ1qAIkjte41nbjZSARIcQUBMGgJUHdGH91VGgEgBJSgmoVbSx1myxQfK2awtSnJShUprpQGuaLu413ydSqgyaY09ELKWA5VAZ70BAsK5ZOYzFDPICWk2+wc2lgSOsRxoBc3iH3Oi94kJ0bXVugnclySiiFmJdSIdiSSGllAK24F694QJqTSm2u6aW6zpjSgMkTKCS9BCUaigcjJIrstbefNABlMkVatmTk3jxAGuE91rpzDe+pjajlRoEfegk9bEq+GLC2sAbaO3EpHRSoorNQCDlvjPEh46f2TpPtOlyNdE3Jq7vOsRqFF1Zuvau28G7SRbsvQVV9+aAAKBJsClFPWWluO6MP7prJOV9dAK1CufVvejFwG3VG5N+tFKAv5-q7ZgsDSTqjAYAFSCYgxO6hsH9wIbnWmlDGal08owLPXsR6VMmvI+erpjHT0CPIKpok77G30QqfRLjv6G3-t7dUQ9GneydtBqoUD9FGCMHoiB8T89JNTvofucAJGABWMBvpuYzQeTQ39NCyaQ-Jthin0PIbMKfIjQWQshC062Lez19ppfBI8xgPRqjgqKE+k51bxBZOhmx8pxcLM8dAXx0GPbwOAd8G0XL2Bgvgkc5jFrQnIPcak35sAMXU0LszRPOLFAeiN0gcRzlJ1HYwEy2YgLi3FaqW6YV6o8g+X0ldrp5jlXhn2JqxU+rvGrP8f6yQ6o63g2bd6zAVQukOvWda328JvbBv4pgyNniFrwD0mzvmkeAB5BABdMMElIKo4Z9GcwdYDAnMgtBbSo9MLQPUmPSC0BlLj2gnHau0g-qnOZ+5KCFuLVG4CMByBwDODViHUOYekHIYnQlYiTEf0ItT09ZPyG0FMNUNo2B6R0mwGHEX0mwDHtJORrCDOmeQhZ9Dmj7PISc+MeceOoB5d6dY16IXKBRdkHFzASX0u04A+I6RxX9PR4q-WGrtnHPHZJx17z0AduyNRsFx203RQxcS9pFL4XNuqV2cMw75XBBmdiMh+rtdmuiFOy93r6Pmn-fG47aQM3pgLdW-qpHlydvusZdp0rp38fVeJ9Zxr936fue6-EOX9LfABfoSKN3sT3Jiy54bSLoopgm+e5b97qn-MaflaToPhww-2vm9D+HmXtu8uV9n3TuPsAE-kCT27rXHuufkB53r9v4IBdZ97PP+q+eiikFT7fZvp-W8FunwL2-j+C+8pX2Hb-suPuG+neVejujOteLu9eyet4T+2uE+5+goj2y2XePU+2xkKAFsoAAApKADKKnCbvfr4KPkfi-hgGfnzh-jnvHIHoQZoCHpbmHtLvnvCEAA)
