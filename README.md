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

A mechanism for hierarchically encrypting triple stores

# 1 Introduction

WNFS

- Scope: read control only; writes are out of scope of THIS spec

## 1.1 Motivation

Facts in a Datalog or RDF database 

While possible to design a system that can query by any field, all available top-down solutions trade off trust (hexastore, layered range trees) or performance (FHE, zkSNARKs).

Cryptable assumes that data will most frequently be granted heirarchically. This is notably often not how data is _accessed_, but does make for a simple mental of what is being shared. It is assumed that in granting access to "everything" about an entity, that this implies all of its fields. For example, granting access to all records with a `name` attribute, or where the value field is set to `Nara` without access to the rest of the entity are less common. As such, favouring the common case for granting read access is reasonable.

TODO Time: snapshot, ranges

The design outlined in this specification MAY be extended by futher splitting data manually across multiple stores.

Once data is retrieved and decrypted, it MAY be indexed locally (e.g. hexastore). 

# 2 Query Dimensions

One of largest challenges with encypting datalog facts is that the access patterns are not known in advance. While it's possible to structure EAV(C) fields as an orthogonal $n$-dimensional tensor, and query in any order, this has major drawbacks. Representing data in this way tends to rely on duplication, indexing, and/or cyclical cross linking. This is not feasible in a Byzantine threat model.

To make the problem tractable, access patterns are broken into two very broad categories which often interact: tabular heirarchy

### 1.2.1 Tabular Heirarchy

For the pruposes of this design, we treat the quad store as a triple store $\langle e, a, \langle v, c \rangle \rangle$.

An intuitive access control layout is following the order:

``` mermaid
erDiagram
    User ||--|{ Store: owns
    Store ||--|{ Entity: contains
    Entity ||--|{ Attribute: contains
    Attribute ||--|{ Value: contains
```

### 1.2.2 Temporal Access

On tables

### 1.2.3 DAG History

History in systems like PomoDB are represented as an acyclic hash graph. While there are several techniques (k-anonymity, OT) that make it possible to search directly on history, they typically to require local secondary indices (or FHE). As tabular data access control MAY 

Reading a CID in the `causedBy` field of a quad MUST NOT immedietly grant access to the entire transative history. While this is an important access pattern in many graph queries, it is not desired for tabular queries. Typically the shape of graph data is more important in queries with a small number of common entities and attributes. Granting access to the entire history of those paths is thus viable.

## 1.x 

- Manage as few keys as possible
- Flexible enough to store in multiple ways (i.e. the data layer below this)

- Sensible defaults: Simple rules _inside_ a store (though stores can be broken up arbitrarily to more custom control)

TODO bring the data/object table from WNFS here

## 1.x Lookup Performance

- Raw decryption speed
- Seek on large stores
- Small network footprint whenever possible

# 2 Heirarchical Encryption

``` mermaid
flowchart TD
    classDef virtual stroke:#333,stroke-dasharray: 5 5;

    seed --> store:::virtual

    subgraph Virtual
        store --> ent1:::virtual
        ent1 -..-> ent2
        ent1 ~~~ attr1-1:::virtual
        ent1 ----> attr1-1
        ent2 --> attr2-1:::virtual
        ent2 ~~~~ attr1-1
        attr1-1 -.-> attr1-2:::virtual

        val1-1-1:::virtual
        val1-2-1:::virtual
        val1-2-2:::virtual
        val1-2-3:::virtual
        val2-1-1:::virtual
        val2-1-2:::virtual
    end
    
    subgraph Keys ["Derived Keys"]
        key1:::virtual
        key2:::virtual
        key3:::virtual
        key4:::virtual
        key5:::virtual
        key6:::virtual
    end

    attr1-1 --> val1-1-1 --> key1{"🔑1.1.1"} --> val1
    attr1-2 -----> val1-2-1 --> key2{"🔑1.2.1"} --> val2
    val1-2-1 -.-> val1-2-2 --> key3{"🔑1.2.2"} --> val3
    val1-2-2 -.-> val1-2-3 --> key4{"🔑1.2.3"} --> val4

    attr2-1 -------> val2-1-1 --> key5{"🔑2.1.1"} --> val5
    val2-1-1 -.-> val2-1-2 --> key6{"🔑2.1.2"} --> val6

    subgraph Concrete
        val1("(ent1, attr1-1, val1, [])")
        val2("(ent1, attr1-2, val2, [])")
        val3("(ent1, attr1-2, val3, [])")
        val4("(ent2, attr1-2, val4, [cidX, cidY])")
        val5("(ent2, attr2-1, val5, [cidX])")
        val6("(ent2, attr2-1, val6, [])")
    end
```

- Justify the order
- explain why no cross linking shenannigans

# 3 Key Derivation

Skip ratchet, but different use from WNFS

A store MUST be seeded with a random nonce of at least 128 bits.

A cryptstore MAY have an unlimited number of levels, but at minimum it MUST contain the following levels:

- StoreRoot
- Entity
- Attribute
- Value

Being granted access to a 

MUST be equipped with a one-way merge function that takes two or more keys and deterministically derives a new value. Concatenating and hashing with SHA2-256 or BLAKE3 is RECOMMENDED.

## 3.1 Vertical Derivation

Derivation of 

## 3.2 Horizonal Derivation

To lock any level to a single version, merge the 

# 4 Semantic Collison

A field of a particular value MAY be assigned multiple times. 

# 5 Prior Art

- Skip Ratchet & WNFS

# 6 FAQ

## 6.1 What About Fully Homomorphic Encryption?

## 6.2 Why Not k-Anonymity?

## 6.3 Why 
