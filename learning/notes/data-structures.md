# Notes — Tuples, Lists & Dictionaries

**Track:** Data Science & Machine Learning
**Topic:** Python data structures fundamentals

---

## 1. Lists

A **list** is an ordered, **mutable** (changeable) collection of items. You can add, remove, or modify elements after creation.

### Syntax

```python
fruits = ["mango", "banana", "pineapple"]
```

### Common Operations

```python
fruits = ["mango", "banana", "pineapple"]

fruits.append("papaya")        # add to the end -> ['mango', 'banana', 'pineapple', 'papaya']
fruits.remove("banana")        # remove by value -> ['mango', 'pineapple', 'papaya']
fruits[0] = "orange"           # modify by index -> ['orange', 'pineapple', 'papaya']
fruits.sort()                  # sort in place
print(fruits[1])               # access by index -> second item
print(len(fruits))             # number of items
```

### Application

- Storing a dataset's rows before converting to a DataFrame
- Collecting results from a loop (e.g., predictions, scores)
- Any situation where the collection needs to grow, shrink, or be reordered

---

## 2. Tuples

A **tuple** is an ordered, **immutable** (unchangeable) collection of items. Once created, you cannot add, remove, or modify elements.

### Syntax

```python
coordinates = (6.1319, 1.2228)  # e.g., Lomé's latitude/longitude
```

### Common Operations

```python
coordinates = (6.1319, 1.2228)

lat, lon = coordinates          # unpacking
print(coordinates[0])           # access by index -> 6.1319

# coordinates[0] = 5.0          # ❌ this would raise a TypeError — tuples can't be modified
```

### Application

- Representing fixed data, like a GPS coordinate or an RGB color `(255, 0, 0)`
- Returning multiple values from a function
- Dictionary keys (tuples are hashable, lists are not)
- Any data that should stay constant to avoid accidental changes

---

## 3. Dictionaries

A **dictionary** is an unordered (technically insertion-ordered since Python 3.7+) collection of **key-value pairs**. It's mutable, and lookups by key are very fast.

### Syntax

```python
student = {"name": "Othniel", "track": "Data Science & ML", "year": 2026}
```

### Common Operations

```python
student = {"name": "Othniel", "track": "Data Science & ML", "year": 2026}

print(student["name"])              # access by key -> 'Othniel'
student["year"] = 2027              # update a value
student["environment"] = "taiss2026"  # add a new key-value pair
del student["year"]                 # remove a key
print(student.keys())               # all keys
print(student.values())             # all values
```

### Application

- Storing structured records (like a JSON object or a database row)
- Counting occurrences (e.g., word frequency in NLP)
- Configuration settings (e.g., model hyperparameters: `{"learning_rate": 0.01, "epochs": 10}`)
- Fast lookups by a unique identifier (e.g., mapping user IDs to user data)

---

## 4. Key Differences

| Feature | List | Tuple | Dictionary |
|---|---|---|---|
| Ordered | Yes | Yes | Yes (insertion order, Python 3.7+) |
| Mutable | Yes | No | Yes |
| Syntax | `[ ]` | `( )` | `{ }` (key-value pairs) |
| Access | By index | By index | By key |
| Duplicates allowed | Yes | Yes | Keys: no / Values: yes |
| Use case | Growing/changing sequences | Fixed, constant data | Labeled, structured data |
| Performance | Slower lookup (linear search) | Slower lookup (linear search) | Fast lookup (hash-based) |

### Quick rule of thumb

- Need an **ordered, changeable** sequence → **list**
- Need an **ordered, fixed** sequence that shouldn't change → **tuple**
- Need to **label** your data and look things up by name/key → **dictionary**

---

## 5. Practice Ideas

- Convert a list of tuples (e.g., `[("mango", 3), ("banana", 5)]`) into a dictionary using `dict()`.
- Write a function that returns a tuple `(min, max, average)` from a list of numbers.
- Build a word-frequency counter using a dictionary from a paragraph of text.
