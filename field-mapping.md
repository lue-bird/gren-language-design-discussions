### field mapping
Introducing syntax for changing one specific field value

```elm
!Name :
    -> (-> value -> valueMapped)
    -> , record, #Name value
    -> , record, #Name valueMapped
```

which one previously had to write as

```elm
(\alter -> \record -> { record | name = record.name |> alter })
```

  - verbose
  - really hard to scale (nested lambdas, branching altering different fields, indentation 🚀)
  - possibly confusing for beginners (what does `|` mean? why are consecutive fields updated with `,`, nested updates look awful, ...)


```elm
model
    |> !PlayerPosition (!Y (\_ -> 0))
    |> !PlayerVelocity
        (\velocity ->
            velocity
                |> !X (\* 0.95)
                |> !Y (\* 0.95)
                |> !Y (\- 1)
        )
    |> checkForCollision
```


Apart from syntax, the proposed syntax also allows changing the field value's type

```elm
--  or ()
Blank =
    #Blank

succeed (, #Name #Blank, #Status #Blank, #Metadata metadataDefault)
    |> field !Name "name" string
    |> field !Status "status" string
```

  - simple
  - declarative `succeed` showing the final shape
  - field value changes are drastically simpler and less verbose

To discuss
  - symbol
      - 🆚 `!`
      - 🆚 `/`
      - `!`, `/` look like "action"
      - `!` looks like `.` (field access) with something else on top
      - _subjective_ `/` is hard and confusing to read with lambdas
  - representation
      - 🆚 function
      - 🆚 `infix`
      - `infix` is slightly more compact
      - function reads better alongside other transformations
      - `infix` can't be curried
      - all `infix`es are just symbols, `!<Name>` would be confusing
      - → a function solves the problems well enough
        to not need a more complex system around it

## extras

### if 1-field records and 1-variant choices unify

```elm
Pet specificProperties =
    , specificProperties
    , #Name String
    , #Hunger Progress

Cat =
    #Cat (#NapsPerDay Float)

Dog =
    #Dog (#BarksPerDay Float)

sit : -> Dog -> Dog
sit =
    !Dog (!Hunger (Progress.by 0.01))

howdy =
    #Cat (, #Name "Howdy", #Hunger Progress.begin, #NapsPerDay 2.2)

howdy |> sit -- error
```
removing the intermediate need for boilerplate.

### field mapping composition

```elm
module Dog exposing (hungerAlter)

Dog =
    #Dog (, #BarksPerDay Float, #Hunger Progress)

hungerAlter : -> (Progress -> Progress) -> (Dog -> Dog)
hungerAlter =
    !Dog << !Hunger
```
Above might be prone for abuse to over-nest your structures and rely on primitives too often.

It might be best to add a few paragraphs on the dangers of the pattern.
