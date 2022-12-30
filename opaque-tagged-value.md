## opaque tagged value

How to integrate opaque types into the idea of module-independent "tagging"

```elm
Password =
    internal#Password String
```

Extraction and creation via `<opaque keyword><tag leading symbol><tag>` will then only be allowed in the same `module`
  - `<type> <arguments> = <opaque keyword>` shows that it's _implementation is hidden, not it's type_

to discuss
  - `<opaque keyword>`
      - _vs_ `opaque`
      - _vs_ `internal`
      - _vs_ `module-internal`
  - if opaque types will be available
      - _vs_
        allow pattern matching
          - allows keeping aspects in sync while still granting access
          - for apps, there's no downside to also allowing read access
      - _vs_
        forbid pattern matching
          - hide implementation → no breaking change on internal structure change
          - allow limiting access for secrets, passwords, personal information
