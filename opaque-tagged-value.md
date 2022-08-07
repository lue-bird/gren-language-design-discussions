## opaque tagged value

```elm
Password =
    internal#Password String
```

to discuss
  - `<opaque keyword>`
      - 🆚 `opaque`
      - 🆚 `internal`
      - 🆚 `module-internal`
  - if opaque types will be available
      - 🆚
        ```elm
        Password =
            internal#Password String
        ```
      - 🆚
        ```elm
        internal Password =
            #Password String
    ```
    Extraction and creation via `<opaque keyword><tag leading symbol><tag>` will then only be allowed in the same `module`
      - `<type> <arguments> = <opaque keyword>` shows that it's _implementation is hidden, not it's type_
