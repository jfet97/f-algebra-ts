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
[With another simplified encoding of HKTs](https://www.typescriptlang.org/play?jsx=0&ts=5.0.0-dev.20230128#code/PTAEAcEtwUwKAGYFcB2BjALpA9ii0YAeNXAZw1AEEA+ACgEMAuKgSmcoG5FVMc8pYxMhUoAaUACE6TKuPoAjZg3YtQAXmqS2krsnRZc+QSRTlZk8QGFp7OYtDLW6zRPHy0S+xNUbQl7Za6PAb8BEKmIuKufuIAInRwAJAyYkkKSik+LqJJ7p7M3s4xSWgAJkoeflmgsTnasUH6fEZEJmZiFjE14gCiCcm2afaOlNWuuZW0XtWWOYllFcz+RbVwoKClMErlNdU9dcw9jbyGAjC0acyoANYo2ADuKHMKAPzMAGLBfHPub6CfTVwczKfwBJyeaw2MFBX1wcG0Nzuj1AAG8kqR7pAMGgABYOegAJwA5kgALYwFAYUgAOgANhSiRgcao0Yl5vRSDBQABGRhJNkEmAYJAEvD0LhstAcrkAJj5bMSguFotACgAhAwWBL2ZzQABmeUCoUivDuDXqzVa-lS3UAFkNiuNKrKGrNDHk5pYXq463WNq5AFYHUqTVCNS6pmhzR7Ld7+ZsEPQkLSMMxWWz6RQlepVcSyRSqQBtAAMAF1tYkENgCQ5M6BIDnuRx66BCLmSeTKTT6ShGTjm5AANSDln8o0UNTt-NdwuQUu0JVWhUAXzHIZVSu1q8Sq9XIFAAFoj8eT6ez+e4JfL5BKTACYm0FyABIAaQAKqjIQB9ShXFC3B4UB9UAvx6P8AMeYCMAAT1gcCkSAuBVzgG8MDvB8uTBDBq0Id5QBgAAPNCUFKUhQFfN9NDRdZBXoUpcFpaDQFJehwGYQgOmiPpaE5WkEGYF8b1KXDenEGhxH4-EVCKQpfEEkiRNAfZJGoJDLxg2BQHk4S8MI4jSPI99RKoTRJ1oPCADJURA39zFAw5QGXFhCwAIg0mAXPLS99wQSACMvNBaQ5Mj3j83D8KIikDIo3oqMhNoMAJJBMGrWhwCQeRaUgNBQFo+iUEY0AADd6FpJAti0oTFOU0KCOq6hqBZRy1O4QE8F8urdMikiyJipS6BKsqKu06rxFq+rGo+ML3li1rwVADraEG8rmHoFBoO0NamOo3KnTwFAYHuf4-KW0ryqtZC9Hm1AOvCvSot6oz+toNCCVJKbOti7QRpmpSxum2LVKukJQBuk7Xve1V1s29bPxovbQAh6llpgLhkPcqhaSJGB5AJeg7u66KnuUmgc1oRMBKq36ScaopOEvYHmilDB8a6-THrfYyaHMj5ghw95qDkLH2CxnG8dGkztFoSAPvq6pKDmkHmfocnVvWoWiTVja4d9dc8FoAiteqUqiXJ6kWPAWgwYIg2WHEZXyY1r0WBa-csvIUhr1ve96EfUAADlIFpPCdryhimK-FnNdAAByQPg5j12wB6esyKZLkMewBBEZxLkUbIm8c65d2MDgfdKFTovQClAkCUgO9Edg+BUPQ32uUsMh3kIGmdbDgqI6j5gY470x3kT+G6PD4qzoqnpIT7wqa7ru92BahLQBQIPJNoap47wszqloFEQMH2O95jpy4HXtpJO7sS6FnuRpN8EfSC7-ZSbMmBRHoQ+dpR5g394qEmXgSVaOR1iR3oNHFyr93guSQi7OAGMAAykByDv3vjmPeoAAA+fhO53xMl7V6GFQBoIwRRCK7NDIflDjASe-dG5wXIegjAXcmToNcqBTy4hOGkG4ZQTy1A0YBWEKAEukksI4QoewiipkdYWyUCxCSh8WLUigUSdQahJxxyDmPUALxmL0FAMwG+tB1EowkhY+g1Il71wJF6UALt0ZN1YeQbuCjxqyPeH1PoYiIgbyDkoaosjCAHSKneBRO8iiLU3nxHeLtr5kHYtxR+EjmBhL6KEthniybf1pNURa5jv4SK9N5MAJdVSi1xhyCpxjrhchMVUk2Yt6BX3EbIygNS8apNENzXAe8Ql03ELgOBSgtjKSYKMOm8sen4x8X4rBZlBn6NGSgOBh8S7vGqDtSACBaDbI0VHbRoAXJ7xcqoPWoBVnBx3pCGAtJdTXLGZ3Q5bD3jIxnuII59i7wuPqR-UAmJaS0lAPILkN5yBrSwPQGApRVRkVINgQwHJ3EYG6djWplcpSgvheIZFwKuQ4noJExG2AIDYHIOAXAGdc6QBrFClmlJID0BCGXMASBSA3i0c0+gpJ5ClBMdhUARI2W5xrOnXKkAiQ4goCYNASoM5uPzqqNAJACSlB5eS4FEqmlinmZXLl8L6kx0oDHG52cY4+KUlQC16ClKFlLB0wJlBa70GgpitpOZPExN8F0+Zd8ehOoSOsP1mgnXiH3Lc94kJTpDXtiAhx1RCxWNANSDNfyCSllANGjZnd4SmvNZa2ONqU7FodSgMkEKCS9GYlyig90eob2rXeF1ZgADKZIvXYsnN3ahD0W0CobpOKtw6CR0ByeQHt4tlJjpraGhw1RixRrADGuNaas3VBRqAQc1ck0NzzXAwtHLY5motVnUtHzbUVrIvOu8dbSQNoHc2+9BJ20UAAAoEmwKUGdJi+0pybQZN9OY32TqKAGrFs7xDgYuGG6o3JV03JQHvDd3z9212TUUHdAAqTDoDc1rvzaPE9+5z0lutde8t9qkWJR5R+0AHb6O9n-T67iU6MWBuUuQOuvZF3hrOfElyyH11hs3QexxRRmN8dNijVQe6XKMEYC5XdBGHFEZQ8epB+5wA-oAFYwHmsp21B5ND700BRy9VGME3to1eswY9GPfuwIZ8EbHANjToHp1zRmQgTMYD0aoOzINsP-UQsJAtF3xNE6h-R6GE3qf+UUNoaUDN+b4PG86iasPJaPQWnTYArNWrLXayuNmKA9ETuvb98LspspgB51s3E9N1eZhVWgkzqjyBRfSNanHwvKSyQ1eDQTaSxbQ+JjDW6iita1e1rLMBVBGNSxJ3LUmzGSc0680jhWIr8vAPST2jGS4AHkED+1bQSUgmTcngZzKlgM9syC0FtM90wtA9TvdILQGU33aBIbG4k52SDT1urxp6w1aFyBwDOKNs7F2rukAgfulm7zyAIDEu6yH0GVZehR7QUw1Q2jYHpHSbApsidkbAF20kbHoelzh5CBHl3x3I+AWjyR4hafhbg-juNKBidkFJzAcnlOXZ7Zc3+qHMAYdM-WCzpHKOHZc9AFL-9iSCeC5S8LsntIKeE4l-UmTPL6ey8ZwQeHbDzus5rezv0bKVaq5N6x+Zmu42kCF6YEXYueJG9PS5tzIQzdy8t8z63iO2fK8d+jjAmO1fpfc27giRQU-4e5MWfnYaidFFMNHznbD4-g49RrrPDgc++BJ3rg3VPJeJ+DzL0PsArfkBt0rjnTvC-iEDxl3AGuXdEjLzxT3RRSD29R53jHWOIel7th7kflfdei-16bMf-v9w96T7jxG5vYdh4VxH23d5x8q67wntrDWNcp98BIXrDCUAG1AAAUlADKZ2WuF+aDzx32PRfsez4-y92RWr0p093hCAA)
