## module header, import

```elm
{: `Dict` convenience operations

## observe

@documentation (, element)

## alter

@documentation (, elementAlter , remove , insert)

## transform

@documentation (, map)

}
module Dict.Extra exposing
    (, element
     , elementAlter , remove , insert
     , map
    )

import Dict exposing (, _ , toList , Dict)
import Stack exposing (, _ , Stack)
import Emptiable exposing (, _ , Emptiable)

toStack :
    -> Dict key value
    -> Emptiable (Stack (; #Key key ; #Value value)) Possibly
toStack =
    \dict -> dict |> toList |> Stack.fromList
```

to discuss
- exposes
    - _vs_ automatically expose all members with `@documentation`
    - _vs_ explicitly list all members in one place
    - un-exposing should be easy but the specific `@documentation` is less easy to find
        - tools can automatically do that on declaration click
    - looking at the header as a `module` author, it should be easy to see all exposed things
- import alias
    - _vs_ allow
    - _vs_ forbid
    - no module has authority to add operations etc. to a namespace, like `import List.Extra as List` → less clutter
    - no alias → simpler (less to learn, easier tooling etc.)
    - when a variable is qualified, you can be sure of its origin
