### type declaration limit

In elm, one can alias _every_ possible type.
Aliasing some kinds of types often leads to unidiomatic code,
which may be reason enough to forbid them.

to discuss
- function
    - _vs_ allow
    - _vs_ forbid
    - makes reasoning about code harder:
      "Why is there another argument if the signature only takes 1?"
    - allows hiding complexity
        - _example_
          [CPS based / van Laarhoven lens](https://kseo.github.io/posts/2016-12-10-encodings-of-lense.html)
          like
          [erlandsona/elm-accessors: Lens](https://dark.elm.dmy.fr/packages/erlandsona/elm-accessors/latest/Accessors#Lens)
        - _example_
          ```elm
          type MapUnmap unmapped mapped
              = MapUnmap
                  { map : unmapped -> mapped
                  , unmap : mapped -> unmapped
                  }
          
          type alias Iso unmapped mapped mappedMapped =
              MapUnmap mapped mappedMapped
              -> MapUnmap unmapped mappedMapped
          ```
    - you might also see both examples
      as prove that hiding the fact it's a function under the hood
      makes composition etc. only seem magical.
      For more complex functions like these,
      the number of type arguments can quickly
      get out of hand.
      Overall, avoiding custom composition operators
      maybe doesn't justify the added complexity?
- record
    - _vs_ allow
    - _vs_ forbid
    - sometimes, record types are declared and used on a variant
      ```elm
      module Expression exposing (Expression(..), LetBlock)

      type Expression
          = LetIn LetBlockInternal
          | CaseOf CaseBlock
      
      type alias LetBlockInternal =
          { declarations : Emptiable (Stacked LetDeclaration) Never
          , result : Expression
          }
      
      type LetBlock
          = LetBlock LetBlockInternal
      ```
  - sometimes, record types are aliases for arguments
      ```elm
      module Simplify exposing (Config(..))

      rule : ConfigInternal -> Rule
      
      type alias ConfigInternal =
          { boolCase : Permission
          , casesToSameExpression : Permission
          }
      
      type Config
          = Config ConfigInternal
      ```
  - almost always, additional context would help greatly
    with type safety and descriptiveness
    (["wrap early, unwrap late"](https://elm-radio.com/episode/wrap-early-unwrap-late/))
      - _example_
        ```elm
        type Direction2d coordinates
            = Direction2d { x : Float, y : Float }
        ```
