# Notes — Functions

**Track:** Data Science & Machine Learning
**Topic:** Python functions — definition, arguments, return values, scope, and best practices

---

## 1. What Is a Function?

A **function** is a reusable block of code that performs a specific task. Functions help avoid repetition, make code easier to test, and break complex problems into smaller pieces.

### Basic Syntax

```python
def greet():
    print("Hello, TAISS 2026!")

greet()   # calling the function -> prints "Hello, TAISS 2026!"
```

---

## 2. Parameters & Arguments

**Parameters** are the variables listed in a function's definition. **Arguments** are the actual values passed in when calling it.

```python
def greet(name):          # 'name' is a parameter
    print(f"Hello, {name}!")

greet("Othniel")           # "Othniel" is the argument
```

### Default Arguments

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Othniel")                    # uses default -> "Hello, Othniel!"
greet("Othniel", greeting="Bonjour") # overrides default -> "Bonjour, Othniel!"
```

### Positional vs. Keyword Arguments

```python
def describe_student(name, track):
    print(f"{name} is in the {track} track")

describe_student("Othniel", "Data Engineering & MLOps")   # positional
describe_student(track="Data Engineering & MLOps", name="Othniel")  # keyword — order doesn't matter
```

### `*args` and `**kwargs`

```python
def sum_all(*args):              # collects extra positional args into a tuple
    return sum(args)

sum_all(1, 2, 3)                  # -> 6

def print_info(**kwargs):        # collects extra keyword args into a dict
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Othniel", track="MLOps")
```

---

## 3. Return Values

A function can send a result back with `return`. Without it, a function returns `None` by default.

```python
def calculate_average(scores):
    return sum(scores) / len(scores)

avg = calculate_average([80, 90, 70])   # avg = 80.0
```

### Returning Multiple Values

Python functions can return multiple values as a tuple:

```python
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([4, 8, 15, 16, 23, 42])   # low = 4, high = 42
```

---

## 4. Scope

**Scope** determines where a variable can be accessed.

```python
x = 10  # global scope

def show_value():
    x = 5  # local scope — only exists inside this function
    print(x)

show_value()   # -> 5
print(x)       # -> 10 (global x is unaffected)
```

Use `global` to modify a global variable from inside a function (generally avoided in favor of return values, since it makes code harder to follow):

```python
counter = 0

def increment():
    global counter
    counter += 1

increment()
print(counter)   # -> 1
```

---

## 5. Lambda (Anonymous) Functions

A **lambda** is a small, unnamed function defined in a single line — useful for short, throwaway logic, often passed to other functions.

```python
square = lambda x: x ** 2
print(square(5))   # -> 25

# Common use: as a key for sorting
students = [("Othniel", 87), ("Ama", 92), ("Kofi", 78)]
students.sort(key=lambda s: s[1])   # sort by score
```

---

## 6. Docstrings

Document what a function does, its parameters, and its return value. This is standard practice, especially for functions others (or future-you) will reuse.

```python
def calculate_average(scores):
    """
    Calculate the average of a list of numeric scores.

    Args:
        scores (list[float]): List of numeric scores.

    Returns:
        float: The average score.
    """
    return sum(scores) / len(scores)
```

---

## 7. Application in Data Science / ML

- **Data cleaning**: `clean_text(text)`, `remove_outliers(df, column)`
- **Feature engineering**: `encode_categorical(df, columns)`
- **Modeling**: `train_model(X_train, y_train)`, `evaluate_model(model, X_test, y_test)`
- **Pipelines**: wrapping preprocessing + training steps into functions makes it easy to reuse across notebooks or plug into an Airflow DAG task.

```python
def train_model(X_train, y_train, model=None):
    """Train a scikit-learn model and return the fitted model."""
    from sklearn.linear_model import LogisticRegression
    model = model or LogisticRegression()
    model.fit(X_train, y_train)
    return model
```

---

## 8. Common Pitfalls

```python
# Mutable default arguments — a classic bug
def add_item(item, basket=[]):   # ❌ default list is shared across calls
    basket.append(item)
    return basket

print(add_item("apple"))   # -> ['apple']
print(add_item("banana"))  # -> ['apple', 'banana']  <- unexpected!

# Fix: use None as default, create the list inside the function
def add_item(item, basket=None):
    if basket is None:
        basket = []
    basket.append(item)
    return basket
```

---

## 9. Summary Table

| Concept | Purpose | Example |
|---|---|---|
| Parameter / argument | Pass data into a function | `def f(x):` / `f(5)` |
| Default argument | Optional parameter with a fallback value | `def f(x=10):` |
| `*args` | Accept variable number of positional args | `def f(*args):` |
| `**kwargs` | Accept variable number of keyword args | `def f(**kwargs):` |
| `return` | Send a result back to the caller | `return x + y` |
| Scope | Where a variable is accessible | local vs. global |
| Lambda | Short, unnamed inline function | `lambda x: x * 2` |
| Docstring | Documents the function's purpose/usage | `"""..."""` |

---

## 10. Practice Ideas

- Write a `clean_data(df)` function that drops nulls and duplicate rows, with a docstring.
- Write a function using `*args` that returns the sum, min, and max of any number of arguments.
- Refactor a function with a mutable default argument bug and fix it.
- Write a `train_and_evaluate(model, X_train, y_train, X_test, y_test)` function that returns accuracy.
