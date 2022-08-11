## comment, documentation

```elm
{: Module documentation

    example
        { Provided by some library
        or as the result of unsafe operations,
        potential bad values have to be "sanitized"
        }
        |> sanitize sanitizeOptionsDefault
        |> add n3
        |> { now we can turn it back }
           toInt
        |> { now we are in unsafe space again }
           \^ -2
    { |> Expect.equal 4 }

## transform

@document map, fold

}
```

- comments in one line
    - _vs_ unique single-line comment syntax `**`/`--`/`,,`
    - _vs_ "re-using" multi-line comments `{  }`/`[  ]`
    - adding line breaks while in a comment just works with multi-line syntax in one line
- documentation comment syntax
    - _vs_ `:`
    - _vs_ `|`
    - `:` can be understood as "explanation:"
    - `:` mentally connects documentation to types
