## module header, import

```elm
{: `Dict` convenience operations.

## transform

@documentation (, map)

## scan

@documentation (, element)

## alter

@documentation (, elementAlter , elementRemove , insert)

}
Dict.ExtraOrdered :
    -> (-> (; #A key ; #B key) -> Order)
    -> (, map
        , element , elementAlter , elementRemove
        , insert
       )
Dict.ExtraOrdered order =

Dict(, _ , toList , Dict) = import DictOrdered order
Stack(, _, Stacked) = import Stack
Emptiable(, _, Emptiable) = import Emptiable

toStack :
    -> Dict key value
    -> Emptiable (Stacked (; #Key key ; #Value value)) Never
toStack =
    toList >> Stack.fromList
```

to discuss
- exposes
    - _vs_ automatically expose all members with `@documentation`
    - _vs_ explicitly list all members in one place
    - un-exposing should be easy but the specific `@documentation` is less easy to find
        - tools can automatically do that on declaration click
