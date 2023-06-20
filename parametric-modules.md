> Incomplete! Just a collection of a few thoughts

The [0.3 live stream](https://youtu.be/Fly0y4lFgDc?t=1488) got me thinking about some details of parametric modules.
I haven't looked at how other languages do it, so this is me exploring :)

To not confuse anyself:
I'll talk about the latest-presented proposal, which is different from the original issue:

## changes from the original [proposal](https://github.com/gren-lang/compiler/issues/81)

  - old approach:
    ```elm
    import Dict(String.AToZComparable) as DictAToZOrder
    DictAToZOrder String User
    ```
    ```elm
    module Dict requires (Comparable)

    type Dict Comparable.T v = ...
    get : Comparable.T -> Dict Comparable.T v -> Maybe v
    ```

  - new approach
    ```elm
    import Dict(String.AToZComparable) as DictByStringAToZ
    DictByStringAToZ User
    ```
    ```elm
    module Dict requires (Comparable as Key)

    type Dict v = ...
    get : Key.T -> Dict v -> Maybe v
    ```
  
That just makes sense and I see no advantages of the previous approach.

## First, some names

- `Comparable`, `Hashable`, `Equatable`, ... I will call these properties from now.
  Just because i need a name to refer to this kind of thing.
- a module exposing the necessary things of a property (like exposing `compare`).
  I will call this "supporting" that property
- a module that takes a concrete module that supports certain properties.
  I will call this "requiring" a module supporting these properties

I will use the syntax as describes below. Do not read this as a specific proposal;
I just want nice and consistent syntax to illustrate my points.

### explicit `supporting` on modules

You wouldn't want to rely on your knowledge all the necessary things are implemented for a property to work, right?
Adding an explicit `supporting ...` before the `exposing` helps to let you know what you still have to implement etc.

Not having `supporting` there is a bit like implementing a java interface without declaring so to your users and yourself.

```elm
module Int
    supporting (Addable, Comparable, ...)
    exposing (Int, compare, add, ...)
```

### `requires` → `requiring`

```elm
module Dict
    requiring (Key supporting Comparable)
    supporting (Equatable)
    exposing (Dict, ...)
```
I would be fine with `requires`-`supports`-`exposes`, too. Just make it read consistently.

### a module with multiple properties?

```elm
module Dict
    requiring ((Comparable, Hashable) as Key)
    exposing (Dict, ...)
```

I'd say if anything this should be

```elm
module Dict
    requiring (Key supporting (Comparable, Hashable))
    exposing (Dict, ...)
```

which reads a lot nicer because you don't want a `Dict` requiring something hashable comparable called ...
but you want a `Dict` with a `Key` that has certain properties

### "signature" module defines what?

Any person looking at the module will notice that it's all type signatures.
But what are these signature used for? I will use the keyword

```elm
property module Comparable

type alias T
compare : T -> T -> Order
```
and show a compiler error if a name other than `T` is chosen.

Now,

## specific points

Let's start with the easy to understand ones

### forces you to expose an API that's too generic

elm is pushing you towards domain-tailored APIs with descriptive names.
Parametric modules (and typeclasses) push in the opposite direction.

I'll give a few extreme examples just to show what people might do with this.
```elm
module String supporting Stringifiable exposing (toString)
toString = identity
```
I mean it would be bad if `String` didn't support that, right?
Then again, looking at the module documentation, who tf would use `String.toString`? Seems like clutter.
```elm
module List supporting Monad exposing (concat, combine, ...)
combine = concat
```
kinda abstract. Haskell names are kinda a perfect example of derailing in that direction.
```elm
[ a, b, c ] |> SetList.sum

module Set supporting Monoid exposing (Set, add, union, addNeutral, empty, ...)
add = union
addNeutral = empty
```
ok now I've gone crazy (like maths) but I hope you got the idea.

I would actually not be unhappy if all these things needed to support properties were by default inaccessible
and only callable by those `module`s that are given the `module` as an argument.

Given this only makes it harder, not impossible to misuse.

### are there always good "default" implementations?

```elm
module Char supporting Comparable exposing (Char, compare, ...)
```
But for types like `Char` there's no one-size-fits-all `compare`.
Let's say you compare by their unicode codes.
Then you might have users shocked that uppercase is less than lowercase etc.

I think that is excusable but I wouldn't mind a bit more explicitness.
Maybe that means not letting `Char` support `Comparable` directly
and instead creating separate `module`s for basic options for comparing, idk.

### not all operations require certain properties

For example, if I want to create a helper

```elm
userAgeDict = \users ->
    users |> Dict.map (\_ -> User.age)
```

that would work with any key order – it's not possible.

Note that you don't need an order function to define `map`
(as well as `empty`, `singleton`, `toList`, `fold`, `keys`, `values`, ...).

You could absolutely shrug this up as unfortunate and I would only shame you a tiny bit,
so let me show the same problem in a different light:

### only one operation requires certain properties

```elm
module List exposing (sum, product, ...)

sum : (a supports Addable).?? List a -> a
product : (a supports Multipliable).?? List a -> a
```

What's "obvious" is that
`List a` shouldn't require that elements generally support `Addable`, `Multipliable`

This issue has already been mentioned in [this comment](https://github.com/gren-lang/compiler/issues/198#issuecomment-1465034737)

I would guess we could do
```elm
import List
import List(Int) as IntList

[ "abcde", "abc", "ab" ]
    |> List.map String.length
    |> IntList.sum
```
That doesn't seem half bad! It's just a bit weird to be able write this two ways:
```elm
import List
import List(Int) as IntList

[ 5, 3, 2 ] |> IntList.map String.fromInt
[ 5, 3, 2 ] |> List.map String.fromInt
```

### generic properties for generic structures?
As an extension of the previous point,
let's try to supply a module with properties to construct a module with properties.

```elm
module List supporting Comparable?? exposing (compare, ...)

compare : (a supports Comparable).?? List a -> List a -> Order
```

What's "obvious" is that
`List a` doesn't need general support for `Comparable`. only `compare` does → see previous section.

To supply a specific property module, I would guess we could do
```elm
import List(Int) as IntList
import Dict(IntList) as DictByIntList
```
That is an ok solution, it just has to be supported.
Plus, it looks a bit weird that you can do
```elm
import List(Uncomparable) as UncomparableList
-- List says it's supporting Comparable
-- but that's not true if we give it something that doesn't support `Comparable`
NeverList.compare NeverList.empty NeverList.empty -- compiler error
```
That's ok but a bit confusing.

----

At this point I would suggest a solution but
we all already know I'm against implementing parametric modules
and in favor of a supplying an explicit type class for every call that needs it, like [`KeysSet`](https://dark.elm.dmy.fr/packages/lue-bird/elm-keysset/latest/)
