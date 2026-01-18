# DataRec Datasets

This repository hosts example datasets organized by data type and serialization
format, used to validate DataRec loading/parsing logic. The structure is meant
to be public documentation and is designed to be easily extended.

## Directory conventions

Datasets are organized as:

```
<kind>/<format>/<task>/dataset.<ext>
<kind>/<format>/<task>/dataset_no_header.<ext>
```

Where:

- kind: `sequences` or `transactions`
- format: serialization format (e.g., `tabular-inline`, `jsonl`)
- task: `interactions`, `ratings`, or `timestamp`
- ext: `tsv` or `json`
- dataset_no_header: optional, headerless variant for tabular data

## Field conventions

Common fields across datasets:

- `user`: user identifier (string or integer)
- `item`: item identifier (string or integer)
- `rating`: preference value (integer/float)
- `timestamp`: temporal signal (string or integer)

## Sequences

Sequence datasets store per-user ordered lists. They live under `sequences/`.

### Tabular (inline)

`sequences/tabular-inline/<task>/dataset.tsv`

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

`sequences/tabular-wide/interactions/dataset.tsv`

- One row per user
- Each item is a separate column

Example:

```tsv
user	item
0	1	2	3
1	1	2	4
```

### Tabular (implicit)

`sequences/tabular-implicit/interactions/dataset.tsv`

- First value is `user`
- Remaining columns are items
- Optional headerless variant: `dataset_no_header.tsv`

Example:

```tsv
user	item
5	1	2	3	6	7
3	1	2	4
```

### JSON (user-indexed)

`sequences/json-user-indexed/<task>/dataset.json`

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

`sequences/json-array/<task>/dataset.json`

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

Transaction datasets store one event per row/object. They live under
`transactions/`.

### Tabular

`transactions/tabular/<task>/dataset.tsv`

- One row per event
- Optional headerless variant: `dataset_no_header.tsv`

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

`transactions/json/<task>/dataset.json`

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

`transactions/jsonl/<task>/dataset.json`

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

1. Create a new folder under `sequences/` or `transactions/`.
2. Add `interactions`, `ratings`, and/or `timestamp` subfolders.
3. Include a minimal `dataset` file with representative samples.
4. Update this document with a new subsection and example snippet.
