# Cryptable v0.1.0

## Editors

- [Quinn Wilton], [Fission Codes]
- [Brooklyn Zelenka], [Fission Codes]

## Authors

- [Quinn Wilton], [Fission Codes]
- [Brooklyn Zelenka], [Fission Codes]

# Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119].

# Abstract

A hierarchically encrypted EVAC tuple store.

# 1 Introduction

WNFS

# 2 Heirarchical Read Control

``` mermaid
flowchart TD
    classDef virtual stroke:#333,stroke-dasharray: 5 5;

    seed --> store:::virtual

    subgraph Virtual
        store --> ent1:::virtual
        ent1 -.-> ent2
        ent1 ----> attr1-1:::virtual
        ent2 ----> attr2-1:::virtual
        attr1-1 -.-> attr1-2:::virtual
    end
    
    subgraph Keys ["Derived Keys"]
        key1:::virtual
        key2:::virtual
        key3:::virtual
        key4:::virtual
        key5:::virtual
        key6:::virtual
    end

    attr1-1 --> key1{"🔑1.1.1"} --> val1
    attr1-2 --> key2{"🔑1.2.1"} --> val2
    key2 -.-> key3{"🔑1.2.2"} --> val3
    key3 -.-> key4{"🔑1.2.3"} --> val4

    attr2-1 --> key5{"🔑2.1.1"} --> val5
    key5 -.-> key6{"🔑2.1.2"} --> val6

    subgraph Concrete
        val1("(ent1, attr1-1, val1, [])")
        val2("(ent1, attr1-2, val2, [])")
        val3("(ent1, attr1-2, val3, [])")
        val4("(ent2, attr1-2, val4, [cidX, cidY])")
        val5("(ent2, attr2-1, val5, [cidX])")
        val6("(ent2, attr2-1, val6, [])")
    end
```

# 3 Key Derivation

A store is begun 
