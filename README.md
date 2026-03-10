# Medical Data Validator

A Python script that validates the structure and content of medical patient records. It checks that each record conforms to expected data types and formats before further processing.

---

## Table of Contents

- [Overview](#overview)
- [Concepts Covered](#concepts-covered)
  - [1. Dictionaries](#1-dictionaries)
  - [2. Looping Over Dictionaries](#2-looping-over-dictionaries)
  - [3. Sets](#3-sets)
  - [4. Python Standard Library & Importing Modules](#4-python-standard-library--importing-modules)
  - [5. Functions & Keyword Argument Unpacking](#5-functions--keyword-argument-unpacking)
  - [6. Regular Expressions for Pattern Matching](#6-regular-expressions-for-pattern-matching)
- [How It Works](#how-it-works)
- [Sample Output](#sample-output)

---

## Overview

The script defines a list of patient records and runs them through two validation functions:

- **`find_invalid_records()`** — checks each field's value against expected constraints and returns a list of invalid field names.
- **`validate()`** — iterates over the full dataset, verifies structure, and calls `find_invalid_records()` for each record.

---

## Concepts Covered

### 1. Dictionaries

A **dictionary** (`dict`) is a Python data structure that stores data as **key-value pairs**. Keys are unique identifiers, and values can be any Python object.

```python
{
    'patient_id': 'P1001',
    'age': 34,
    'gender': 'Female',
    'diagnosis': 'Hypertension',
    'medications': ['Lisinopril'],
    'last_visit_id': 'V2301',
}
```

In this script, each patient record is represented as a dictionary. This makes it easy to look up specific fields by name rather than by position (as you would with a list).

The `constraints` dictionary inside `find_invalid_records()` is also a notable use: it maps field names (keys) to boolean validation results (values), making the validation logic easy to read and extend.

---

### 2. Looping Over Dictionaries

Python provides several ways to iterate over dictionaries. This script uses two common approaches:

**Iterating over `enumerate()` on a list of dicts:**
```python
for index, dictionary in enumerate(data):
    ...
```
`enumerate()` yields both the index and the item, which is useful for reporting *where* an invalid record was found.

**Iterating over `.items()`:**
```python
for key, value in constraints.items():
    if not value:
        ...
```
`.items()` returns each key-value pair as a tuple, allowing you to inspect both the field name and its validation result in a single loop.

Other common dictionary iteration techniques (not used here but worth knowing):
- `.keys()` — iterate over keys only
- `.values()` — iterate over values only

---

### 3. Sets

A **set** is an unordered collection of **unique** elements. Sets are ideal for membership testing and comparison operations.

```python
key_set = set(
    ['patient_id', 'age', 'gender', 'diagnosis', 'medications', 'last_visit_id']
)
```

Here, `key_set` is used to verify that each patient dictionary has **exactly** the required fields — no more, no less:

```python
if set(dictionary.keys()) != key_set:
    ...
```

`dictionary.keys()` returns the keys of the dictionary, which is then converted to a set and compared using `!=`. Because sets ignore order and duplicates, this is a clean and efficient way to check for missing or unexpected keys.

---

### 4. Python Standard Library & Importing Modules

The **Python Standard Library** is a collection of modules bundled with Python that provide ready-to-use functionality. You import them with the `import` statement — no installation required.

```python
import re
```

This imports the **`re` module** (Regular Expressions), which is part of the standard library. It provides tools for matching string patterns, used here to validate the format of IDs:

```python
re.fullmatch('p\d+', patient_id, re.IGNORECASE)
re.fullmatch('v\d+', last_visit_id, re.IGNORECASE)
```

- `re.fullmatch()` checks that the *entire* string matches the given pattern.
- `'p\d+'` means: starts with the letter `p`, followed by one or more digits.
- `re.IGNORECASE` makes the match case-insensitive, so both `'P1001'` and `'p1001'` are valid.

---

### 5. Functions & Keyword Argument Unpacking

The script defines two functions to separate concerns: finding invalid fields vs. orchestrating the full validation.

A key technique used is **`**` (double-star) unpacking** to pass a dictionary's key-value pairs as keyword arguments to a function:

```python
invalid_records = find_invalid_records(**dictionary)
```

This is equivalent to calling:
```python
find_invalid_records(
    patient_id=dictionary['patient_id'],
    age=dictionary['age'],
    ...
)
```

This pattern keeps the calling code concise and decoupled from the function signature.

---

### 6. Regular Expressions for Pattern Matching

Beyond importing `re`, the script demonstrates practical regex usage for data validation:

| Pattern | Meaning |
|---|---|
| `p\d+` | Literal `p` (case-insensitive) followed by one or more digits |
| `v\d+` | Literal `v` (case-insensitive) followed by one or more digits |
| `re.IGNORECASE` | Flag to ignore uppercase/lowercase differences |
| `re.fullmatch()` | Ensures the pattern matches the *whole* string, not just a substring |

This makes the validator robust to common inconsistencies in data entry (e.g., `'P1001'` vs `'p1001'`).

---

## How It Works

1. A list of patient record dictionaries (`medical_records`) is defined.
2. `validate(data)` is called with that list.
3. For each record, it checks:
   - The record is a `dict`.
   - The record has exactly the required keys (using set comparison).
   - Each field passes its type and format constraint (via `find_invalid_records()`).
4. Any violations are printed with the position and field name.
5. Returns `True` if all records are valid, `False` otherwise.

---

## Sample Output

Given the sample data (which contains mixed-case IDs and gender values), the validator will confirm all records pass since `re.IGNORECASE` and `.lower()` handle casing gracefully:

```
Valid format.
```

If a record were malformed — for example, an `age` stored as a string or a missing field — the output would look like:

```
Unexpected format 'age: "thirty"' at position 2.
```
