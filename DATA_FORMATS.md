# DataRec Data Formats

This document describes the dataset formats supported by DataRec. It is designed
to be readable, structured, and easy to extend as new formats are added.

## Index

- [Field conventions](#field-conventions)
- [Sequences](#sequences)
  - [Tabular (inline)](#tabular-inline)
  - [Tabular (wide)](#tabular-wide)
  - [Tabular (implicit)](#tabular-implicit)
  - [JSON (user-indexed)](#json-user-indexed)
  - [JSON (array)](#json-array)
- [Transactions](#transactions)
  - [Tabular](#tabular)
  - [JSON](#json)
  - [JSONL](#jsonl)
- [Extending this catalog](#extending-this-catalog)

## Field conventions

Common fields across datasets:

- `user`: user identifier (string or integer)
- `item`: item identifier (string or integer)
- `rating`: preference value (integer/float)
- `timestamp`: temporal signal (string or integer)

## Sequences

Sequence datasets store per-user ordered lists.

### Tabular (inline)

- One row per user
- `item` contains a delimiter-separated sequence (semicolon)
- Optional `timestamp` column for aligned sequences

Example (`interactions`):

```tsv
user	item
0	1;2;3
1	1;2;4
```

Example (`timestamp`):

```tsv
user	item	timestamp
0	1;2;3	000
1	1;2;4	010
```

### Tabular (wide)

- One row per user
- Each item is a separate column

Example:

```tsv
user	item
0	1	2	3
1	1	2	4
```

### Tabular (implicit)

- First value is `user`
- Remaining columns are items
- Optional headerless variant for tabular data

Example:

```tsv
user	item
5	1	2	3	6	7
3	1	2	4
```

### JSON (user-indexed)

- Top-level object keyed by `user`
- Value is an ordered list of events

Example (`interactions`):

```json
{
  "0": [
    { "item": 1 },
    { "item": 2 }
  ]
}
```

Example (`ratings`):

```json
{
  "0": [
    { "item": 1, "rating": 1 },
    { "item": 2, "rating": 1 }
  ]
}
```

Example (`timestamp`):

```json
{
  "0": [
    { "item": 1, "rating": 1, "timestamp": "001" },
    { "item": 2, "rating": 1, "timestamp": "022" }
  ]
}
```

### JSON (array)

- Top-level array
- Each entry contains `user` and `sequence`

Example (`interactions`):

```json
[
  {
    "user": "0",
    "sequence": [
      { "item": 1 },
      { "item": 2 }
    ]
  }
]
```

Example (`timestamp`):

```json
[
  {
    "user": "0",
    "sequence": [
      { "item": 1, "rating": 1, "timestamp": "001" },
      { "item": 2, "rating": 1, "timestamp": "022" }
    ]
  }
]
```

## Transactions

Transaction datasets store one event per row/object.

### Tabular

- One row per event
- Optional headerless variant for tabular data

Example (`ratings`):

```tsv
user	item	ratings
0	1	1
0	2	1
```

Example (`timestamp`):

```tsv
user	item	ratings	timestamp
0	1	1	001
0	2	1	022
```

### JSON

- Top-level array
- One object per event

Example (`ratings`):

```json
[
  { "user": 0, "item": 1, "rating": 1 },
  { "user": 0, "item": 2, "rating": 1 }
]
```

### JSONL

- One JSON object per line

Example (`interactions`):

```json
{"user": 0, "item": 1}
{"user": 0, "item": 2}
```

Example (`timestamp`):

```json
{"user": 0, "item": 1, "rating": 1, "timestamp": "001"}
{"user": 0, "item": 2, "rating": 1, "timestamp": "022"}
```

## Extending this catalog

To add a new dataset format:

1. Define the new format structure (fields and serialization).
2. Provide a minimal example that exercises the loader.
3. Add a new subsection under the appropriate data type.
