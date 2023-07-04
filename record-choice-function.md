### record, `Array`, choice, function

```elm
Result value error =
    | #Failure error
    | #Success value


Int.atLeast = \minimum \int
    int
        |> Int.isAtLeast minimum
        |> | (#Failure error)
              error |> .Expected |> .Minimum
           \ (#Success value)
              value

Code code =
    ; code
    ; #Imports Imports
    ; #Range (Range2d Int)

countInitial : ; #Count0 Int ; #Count1 Int
countInitial =
    ; #Count0 0 ; #Count1 0

Array.map :
    -> (-> element
        -> elementMapped
       )
    -> (-> Array element
        -> Array elementMapped
       )
    =
    \elementChange \array
        array |> Array.Internal.map elementChange

(, (, 0 , 1 , 2)
 , (, 3 , 4 , 5)
 , (, 6 , 7 , 8)
)
    |> Array.map Array.sum
    |> Array.map String.fromInt
--> , "3"
--> , "12"
--> , "21"
```

To discuss
  - 1 value per tag, disallowing positional values
  - tag mark
      - _vs_ always `#<tag>`
      - _vs_ uppercase `#Tag` represented as `Tag` in expression, pattern
      - _vs_ uppercase `Tag` without leading `#`

      - There needs to be a common syntax for 1-variant choice, 1-field record expressions, patterns, types: `#OneVariant _`.
          - `gren-format` will remove potential unique brackets or separators around single tagged values
          - the tag-value representation can then be used to both extend a union or a record
          - common syntax for field/variant tagging/accessing/mapping
        
        Since, as argued above, this is the case, it would be confusing not to require a leading symbol for all tags
    - `<tag>` casing
      - _vs_ `#tag`
      - _vs_ 
      - most languages lowercase field names, so `#tag`
      - most languages uppercase variant names, so `#Tag`
      - `#<tag>` in types, expressions, patterns is less easy to mix up with variables
      - uppercase `Tag` without leading `#` in types
        is too similar to named types like `Array`
      - uppercase tag forbids field punning
          - points in earlier discussion;
            basically improves descriptiveness and scales better
      - having fields like
        ```elm
        ; #Camera Camera; #Mood Mood; #Lighting Lighting; #Rules Rules
        ```
        _might_, even though it's unambiguous, be confusing: "which is the type, what's the type?"
      - lowercase tag is easier and faster to type
          - `gren-format` can auto-uppercase

  - combining multiple arguments
      - 1-field records, 1-variant choices, only-result functions shouldn't be differentiated
        so their respective syntax shouldn't allow just one argument
      - _vs_ leading symbol per argument: different for record `;`, `Array` `,`, choice `|`, function `->`;
        in `(  )` where necessary
      - _vs_ ` ; ; `, ` , , `, ` | | `, ` -> -> `
          - _vs_ forced `( ... )`
          - _vs_ redundant `( ... )`s being removed
      - _vs_ `< | | >`, `{ ; ; }`, `[ , , ]` (and `[ -> -> ]` or some other brackets for functions, with `[]` being invalid syntax)
      - one symbol in ` | | `, ` ; ; `
        syntactically unifies 1-field record and 1-variant choice
        without further effort
          - allows `.Tag` to access the value
            (and `!Tag` to map the value if it gets implemented)
          - adding another variant in another branch is easier
      - unique brackets plus unique brackets require more extra symbols
        but don't provide more utility than just using different leading separators
      - different brackets means intuitive `<>` = Never, `{}` = Unit
      - unique brackets or leading symbols makes it easy to differentiate record, choice
          - even without, it's probably obvious enough (see function types currently)
      - unique brackets enable `{}` as unit, `<>` as never without separate types
      - same brackets make `{}`/`<>` invalid syntax
          - ```elm
            Blank = #Blank -- magically unifies as empty record
            Name = Named Blank

            Impossible = #Still Impossible
            Weekday = WeekdayAnd Impossible

            case weekday of
                #Monday -> #Monday
                ...
                #Still still ->
                    still |> impossible
            ```
            have to be declared as separate types
      - one symbol in ` ; ; `, ` , , `, ` | | `, ` -> -> ` in `( ... )`?
          - _vs_ forcing `( ... )`
          - _vs_ `gren-format`ting away redundant `( ... )`

        `( ... )` will not be forced on patterns and constructors of 1-field records, variants
          - always requiring `( ... )` is more consistent and obvious:
            ```elm
            Ok Int | Err (List DeadEnd) -> Ok Int | Err String
            Count0 Int ; Count1 Int -> Count0 Int ; Count1 Int
            ```
            compared to
            ```elm
            ( ( Ok Int | Err (List DeadEnd) ) -> ( Ok Int | Err String ) )
            ( ( Count0 Int ; Count1 Int ) -> ( Count0 Int ; Count1 Int ) )
            ```
            telling you there's more to look out for later below the first argument
  - extension
      - _vs_ for leading symbols
          - _vs_ `|| <ext> | `/`;; <ext> ; `
          - _vs_ `| <ext> | `/`; <ext> ; `
      - _vs_ for separators
          - _vs_ `<ext> || | `/`<ext> ;; ; `
          - _vs_ `<ext> | | `/`<ext> ; ; `
      - `;`/`|` is faster to type than `;;`/`||`
      - `||`/`;;` as extra symbols seem unnecessary
      - `;`/`|` might be understood as "next, another single element"
        while `;;`/`||` might be understood as "and" extend with
  - separator between tag and value
      - _vs_ `<tag leading symbol><tag> =/: <construct> <arguments>`
      - _vs_ `<tag leading symbol><tag> (<construct> <arguments>)`
      - `:` requires less `(...)`
      - `<tag>` `:` (of type) `<value>`:
        A tag isn't _of a type_, it has a type _attached_
      - _subjective_ not having a separator reads cleanly, `=`/`:` stop flow
      - _subjective_ not including `(...)` _might_ look like multiple attached values
      - _subjective_ not including `(...)` is harder to parse visually
  - field leading symbol spacing
      - _vs_ `<field>; <field>`, for example
        ```elm
        countInitial : ; #Count0 Int; #Count1 Int
        countInitial =
            ; #Count0 0; #Count1 0
        ```
      - _vs_ `<field> ; <field>`
        ```elm
        countInitial : ; #Count0 Int ; #Count1 Int
        countInitial =
            ; #Count0 0 ; #Count1 0
        ```
      - `<field> ; <field>` is in line with `<variant> | <variant>`
  - force `->` to have 2 arguments?
      - makes currying extremely obvious and explicit
      - discourages reaching for undescriptive positional arguments too often
      - forcing some `(  )`s in types isn't in line with expressions
        where redundant parens are removed
  - pattern matching syntax
      - _vs_ reuse `\` from lambda, list cases with `|`
          - _vs_ format a new-line after `\` before `|`
            ```elm
            \
             | (#Failure error)
                 error |> .Expected |> .Minimum
             | (#Success value)
                 value
            ```
            - [OCaml](https://ocaml.org/docs/data-types#constructors-with-data),
              [F#](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/match-expressions#syntax) use that syntax with `function` instead of `\`
          - _vs_ format `\` and directly after that `|`
            ```elm
            \| (#Failure error)
                 error |> .Expected |> .Minimum
             | (#Success value)
                 value
            ```
          - _vs_ format `\` for the first case and  for the other cases `|`
            ```elm
            \ (#Failure error)
                error |> .Expected |> .Minimum
            | (#Success value)
                value
            ```
            - LambdaCase uses this exact syntax
          - _vs_ format `\` for the last case and for the previous cases `|`
            ```elm
            | (#Failure error)
                error |> .Expected |> .Minimum
            \ (#Success value)
                value
            ```
            - [tao](https://github.com/zesterer/tao) uses this exact syntax
      - _vs_ introduce a new keyword like `match`
          - most languages do it like that
      - [elixir uses `fn` with indentation](https://elixir-lang.org/getting-started/case-cond-and-if.html)
      - one unifying symbol makes it much easier to switch from one tho the other
      - adding reserved makes the language less simple (not by a lot)
      - reusing `\` makes the similarities to lambda obvious
      - _subjective_ `\` looks ok in combination with the indented `|` branches, especially with a new-line between `\` and the first `|`
      - not separating `\` and the first `|` with a new-line is more compact
      - not having `|` for the first of multiple patterns doesn't make it seem like there are more cases to come
      - Having `\` for the last case is a cute visual sign that all cases will have been matched then
      - Having `\` for a specific case makes it harder to move cases around (bad)
  - if
      - _vs_ use pattern matching
        ```elm
        int <= maximum
            |> | #True int
               \ #False maximum
        ```
      - _vs_ also supply
        ```elm
        Bool.if (int <= maximum)
            (; #Then int
             ; #Else maximum
            )
        ```
      - _vs_ custom syntax
      - if should be reached for rarely in favor of pattern matching
          - "parse, don't validate"
          - more descriptive types, values, cases
      - introducing extra sugar brings more work to maintain for tooling and is one extra thing to learn
      - _personal opinion_ There doesn't even need to be a `Bool` exposed from `gren/core`

### declaration

```elm
Translate mapped unmapped =
    #BothWays
        (; #Map (-> unmapped -> mapped)
         ; #Unmap (-> mapped -> unmapped)
        )
```

To discuss
  - `type alias <type>` _vs_ `<type>`
      - no keywords is simpler, less visual noise
      - keywords make it look more distinct from value, function declarations
  - if `; ; `, `, , `, `| | `, ` -> -> ` parens are optional
      - _vs_
        ```elm
        AOrB arguments
            = #A
            | #B
        
        aToB
            :  #A
            -> #B
        
        aAndB
            : #A
            ; #B
        ```
      - _vs_
        ```elm
        AOrB arguments =
              #A
            | #B
        
        aToB :
               #A
            -> #B
        
        aAndB :
              #A
            ; #B
        ```
      - `=`/`:` on the same line is more in line with all other type/value declarations
