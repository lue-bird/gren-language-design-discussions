## opaque tagged value

```elm
Password =
    internal#Password String
```

to discuss
  - `<opaque keyword>`
      - _vs_ `opaque`
      - _vs_ `internal`
      - _vs_ `module-internal`
  - if opaque types will be available
      - _vs_
        ```elm
        Password =
            internal#Password String
        ```
      - _vs_
        ```elm
        internal Password =
            #Password String
    ```
    Extraction and creation via `<opaque keyword><tag leading symbol><tag>` will then only be allowed in the same `module`
      - `<type> <arguments> = <opaque keyword>` shows that it's _implementation is hidden, not it's type_
