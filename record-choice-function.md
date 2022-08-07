### record, choice, function

```elm
Result value error =
    | #Failure error
    | #Success value

case result of
    #Failure error ->
        error.Expected.Minimum
    
    #Success value ->
        value

Code code =
    , code
    , #Imports Imports
    , #Range (Range2d Int)

countInitial : , #Count0 Int, #Count1 Int
countInitial =
    , #Count0 0, #Count1 0

map :
    -> (-> element
        -> elementMapped
       )
    -> (-> List element
        -> List elementMapped
       )
```

To discuss
  - 1 value per tag, disallowing positional values
  - tag leading symbol
      - 🆚 `#`
      - 🆚 `-`
      - There needs to be a common syntax for 1-variant choice, 1-field record expressions, patterns, types: `#/-OneVariant _`.
          - `gren-format` will remove potential unique brackets or separators around single tagged values
          - the tag-value representation can then be used to both extend a union or a record
          - common syntax for field/variant tagging/accessing/mapping
        
        Since, as argued above, this is the case, it would be confusing not to require a leading symbol for all tags
      - `-` _might_ be interpreted as "subtract" or "private"
  - combining multiple arguments
      - 1-field records, 1-variant choices, only-result functions shouldn't be differentiated
        so their respective syntax shouldn't allow just one argument
      - 🆚 leading symbol per argument: different for variant `|`, field `,` in `(  )`
      - 🆚 ` | | `, ` , , `, ` -> -> `
          - 🆚 forced `( ... )`
          - 🆚 redundant `( ... )`s being removed)
      - 🆚 `< | | >`, `{ , , }` (and `[ -> -> ]` or some other brackets for functions, with `[]` being invalid syntax)
      - one symbol in ` | | `, ` , , `
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
      - one symbol in ` | | `, ` , , `, ` -> -> ` in `( ... )`?
          - 🆚 forcing `( ... )`
          - 🆚 `gren-format`ting away redundant `( ... )`

        `( ... )` will not be forced on patterns and constructors of 1-field records, variants
          - always requiring `( ... )` is more consistent and obvious:
            ```elm
            Ok Int | Err (List DeadEnd) -> Ok Int | Err String
            Count0 Int, Count1 Int -> Count0 Int, Count1 Int
            ```
            compared to
            ```elm
            ( ( Ok Int | Err (List DeadEnd) ) -> ( Ok Int | Err String ) )
            ( ( Count0 Int, Count1 Int ) -> ( Count0 Int, Count1 Int ) )
            ```
            telling you there's more to look out for later below the first argument
  - extension
      - 🆚 for leading symbols
          - 🆚 `|| <ext> | `/`,, <ext> ,`
          - 🆚 `| <ext> | `/`, <ext> , `
      - 🆚 for separators
          - 🆚 `<ext> || | `/`,, <ext> ,`
          - 🆚 `<ext> | | `/`<ext> , , `
      - `,`/`|` is faster to type than `&`/`||`
      - `||`/`&` as extra symbols seem unnecessary
      - `,`/`|` might be understood as "next, another single element"
        while `&`/`||` might be understood as "and" extend with
  - `<tag>` casing
      - 🆚 `tag`
      - 🆚 `#Tag`
      - most languages lowercase field names
      - `#Tag` looks distinct in types, expressions, patterns, less easy to mix up with variables
      - `#Tag` forbids field punning
          - points in earlier discussion;
            basically improves descriptiveness and scales better
      - having fields like `( #Camera Camera, #Mood Mood, #Lighting Lighting, #Rules Rules )`
        _might_, even though it's unambiguous, be confusing: "which is the type, what's the type?"
      - `#tag` is easier and faster to type
          - `gren-format` could auto-uppercase the tags
  - separator between tag and value
      - 🆚 `<tag leading symbol><tag> =/: <construct> <arguments>`
      - 🆚 `<tag leading symbol><tag> (<construct> <arguments>)`
      - `:` requires less `(...)`
      - `<tag>` `:` (of type) `<value>`:
        A tag isn't _of a type_, it has a type _attached_
      - _subjective_ not having a separator reads cleanly, `=`/`:` stop flow
      - _subjective_ not including `(...)` _might_ look like multiple attached values
      - _subjective_ not including `(...)` is harder to parse visually TODO
  - force `->` to have 2 arguments?
      - makes currying extremely obvious and explicit
      - discourages reaching for undescriptive positional arguments too often
      - forcing some `(  )`s in types isn't in line with expressions
        where redundant parens are removed

### declaration

```elm
Translate mapped unmapped =
    #BothWays
        (, #Map (-> unmapped -> mapped)
         , #Unmap (-> mapped -> unmapped)
        )
```

To discuss
  - `type alias <type> =` 🆚 `<type>`
      - simple
      - less distinct from value, function declarations
  - if ` | | `, ` , , `, ` -> -> ` parens are optional
      - 🆚
        ```elm
        AOrB arguments
            = #A
            | #B
        
        aToB
            :  #A
            -> #B
        
        aAndB
            : #A
            , #B
        ```
      - 🆚
        ```elm
        AOrB arguments =
              #A
            | #B
        
        aToB :
              #A
            -> #B
        
        aAndB :
              #A
            , #B
        ```
      - `=`/`:` on the same line is more in line with all other type/value declarations
