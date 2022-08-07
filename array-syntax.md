## `Array` syntax

```elm
(- (- 0 - 1 - 2)
 - (- 3 - 4 - 5)
 - (- 6 - 7 - 8)
)
    |> Array.concatMap Array.sum
    |> Array.map String.fromInt
--> - "3"
--> - "12"
--> - "21"
```

