# How to Create a Compound Pattern

A **compound pattern** combines multiple `.pyt` pattern files using logical operators (**AND**, **OR**, **NOT**). The structure is encoded entirely as a folder hierarchy — no configuration file needed.

---

## Folder Structure Rules

| Folder name     | Meaning                                      |
|-----------------|----------------------------------------------|
| Root folder     | The name of the compound pattern itself      |
| `and/`          | All children must match                      |
| `or/`           | At least one child must match                |
| `not/`          | The single child must **not** match          |
| `*.pyt` file    | A leaf pattern (the actual pattern to check) |

---

## Minimal Example

Suppose you want to build a compound pattern called **`HasInitButNoReturn`** that checks:

> A class has an `__init__` method **AND** that `__init__` does **NOT** return a value.

### Step 1 — Create the root folder

```
HasInitButNoReturn/
```

### Step 2 — Add the `and` operator folder inside it

```
HasInitButNoReturn/
└── and/
```

### Step 3 — Place the first pattern file directly inside `and/`

```
HasInitButNoReturn/
└── and/
    └── has_class_init.pyt
```

### Step 4 — Add a `not` subfolder inside `and/`, then place the second pattern inside it

```
HasInitButNoReturn/
└── and/
    ├── has_class_init.pyt
    └── not/
        └── has_init_return.pyt
```

### Result

The compound pattern evaluates as:

```
has_class_init   AND   NOT has_init_return
```

A code file matches only if it has an `__init__` method **and** that method does not contain a `return` statement.

---

## Example with OR

To require that a class has either an early return **or** a direct method return:

```
HasAnyReturn/
└── or/
    ├── has_early_return.pyt
    └── has_method_return.pyt
```

---

## Uploading to Pyttern Visualizer

Once your folder structure is ready, zip the **root folder** (e.g., `HasInitButNoReturn.zip`) and upload it through the Compound Pattern upload button in the visualizer.
