## module header, import

```elm
{: `Dict` convenience operations

## scan

@documentation (, element)

## alter

@documentation (, elementAlter , remove , insert)

## transform

@documentation (, map)

}
Dict.ExtraOrdered :
    -> (-> (; #A key ; #B key) -> Order)
    -> (, element
        , elementAlter , remove , insert
        , map
       )
Dict.ExtraOrdered order =

Dict(, _ , toList , Dict) = import DictOrdered order
Stack(, _, Stacked) = import Stack
Emptiable(, _, Emptiable) = import Emptiable

toStack :
    -> Dict key value
    -> Emptiable (Stacked (; #Key key ; #Value value)) never_
toStack =
    toList >> Stack.fromList
```

to discuss
- exposes
    - _vs_ automatically expose all members with `@documentation`
    - _vs_ explicitly list all members in one place
    - un-exposing should be easy but the specific `@documentation` is less easy to find
        - tools can automatically do that on declaration click
    - looking at the header as a `module` author, it should be easy to see all exposed things
