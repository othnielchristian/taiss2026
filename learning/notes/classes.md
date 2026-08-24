# Notes — Classes

**Track:** Data Science & Machine Learning
**Topic:** Object-Oriented Programming in Python — classes, objects, and their building blocks

---

## 1. What Is a Class?

A **class** is a blueprint for creating **objects** (instances). It bundles data (**attributes**) and behavior (**methods**) together.

```python
class Student:
    """Represents a TAISS 2026 participant."""

    def __init__(self, name, track):
        """Initialize a Student instance.

        Args:
            name (str): The student's full name.
            track (str): The track the student is enrolled in.
        """
        self.name = name
        self.track = track


student = Student("Othniel", "Data Engineering & MLOps")
print(student.name)   # -> "Othniel"
```

- **Class** = the blueprint (`Student`)
- **Object / instance** = a specific thing built from the blueprint (`student`)
- **Attribute** = data stored on an instance (`name`, `track`)
- **Method** = a function defined inside a class (`__init__`, or any custom method)

---

## 2. The `__init__` Method (Constructor)

`__init__` runs automatically when a new object is created. It's where you set up initial attributes.

```python
class Dataset:
    """A simple wrapper around a list of records."""

    def __init__(self, records):
        """Initialize the dataset.

        Args:
            records (list): The raw records to store.
        """
        self.records = records
        self.size = len(records)
```

---

## 3. Instance Methods

Regular methods take `self` as the first parameter, giving them access to the object's attributes.

```python
class Dataset:
    def __init__(self, records):
        self.records = records

    def add_record(self, record):
        """Append a new record to the dataset.

        Args:
            record (dict): The record to add.
        """
        self.records.append(record)

    def summary(self):
        """Return a short summary string of the dataset.

        Returns:
            str: Number of records currently stored.
        """
        return f"Dataset with {len(self.records)} records"


ds = Dataset([{"id": 1}, {"id": 2}])
ds.add_record({"id": 3})
print(ds.summary())   # -> "Dataset with 3 records"
```

---

## 4. Class Attributes vs. Instance Attributes

- **Instance attributes** — unique to each object, set via `self` (usually in `__init__`).
- **Class attributes** — shared by all instances of the class, defined directly in the class body.

```python
class Model:
    """Base class for a simple ML model wrapper."""

    version = "1.0"  # class attribute — shared across all instances

    def __init__(self, name):
        self.name = name  # instance attribute — unique per object


model_a = Model("LogisticRegression")
model_b = Model("RandomForest")

print(model_a.version)   # -> "1.0"
print(model_b.version)   # -> "1.0"
print(model_a.name)      # -> "LogisticRegression"
```

---

## 5. Inheritance

A class can inherit attributes and methods from another class, allowing shared behavior and specialization.

```python
class Model:
    """Base class for ML models."""

    def __init__(self, name):
        self.name = name

    def describe(self):
        """Return a description of the model."""
        return f"Model: {self.name}"


class ClassificationModel(Model):
    """A model specialized for classification tasks."""

    def __init__(self, name, num_classes):
        super().__init__(name)  # call the parent class's __init__
        self.num_classes = num_classes

    def describe(self):
        """Override the parent's describe() method."""
        return f"{super().describe()}, {self.num_classes} classes"


clf = ClassificationModel("RandomForest", num_classes=3)
print(clf.describe())   # -> "Model: RandomForest, 3 classes"
```

- `super()` calls the parent class's version of a method.
- Overriding a method (like `describe()` above) lets a subclass customize inherited behavior.

---

## 6. Dunder (Magic) Methods

Special methods surrounded by double underscores let objects work with Python's built-in syntax (`print()`, `len()`, `+`, etc.).

```python
class Dataset:
    def __init__(self, records):
        self.records = records

    def __len__(self):
        """Allow len(dataset) to work."""
        return len(self.records)

    def __str__(self):
        """Allow print(dataset) to show something readable."""
        return f"Dataset({len(self.records)} records)"


ds = Dataset([{"id": 1}, {"id": 2}])
print(len(ds))   # -> 2
print(ds)        # -> "Dataset(2 records)"
```

Common dunder methods: `__init__` (constructor), `__str__` (readable print output), `__repr__` (developer-facing representation), `__len__` (`len()` support), `__eq__` (`==` comparison).

---

## 7. Application in Data Science / ML

Classes are useful for grouping related state and behavior — for example, wrapping a full ML workflow:

```python
class TrainingPipeline:
    """Encapsulates a simple train/evaluate workflow."""

    def __init__(self, model, X_train, y_train, X_test, y_test):
        """Initialize the pipeline with data and a model.

        Args:
            model: A scikit-learn compatible estimator.
            X_train, y_train: Training data and labels.
            X_test, y_test: Test data and labels.
        """
        self.model = model
        self.X_train = X_train
        self.y_train = y_train
        self.X_test = X_test
        self.y_test = y_test

    def train(self):
        """Fit the model on the training data."""
        self.model.fit(self.X_train, self.y_train)

    def evaluate(self):
        """Score the model on the test data.

        Returns:
            float: Accuracy score.
        """
        return self.model.score(self.X_test, self.y_test)
```

Other examples: a `Dataset` class to hold loading/cleaning logic, a `FeatureEngineer` class to hold transformation steps, or an `AirflowTaskWrapper` class for pipeline tasks.

---

## 8. Summary Table

| Concept | Purpose | Example |
|---|---|---|
| Class | Blueprint for objects | `class Student:` |
| Object / instance | A specific thing built from a class | `student = Student(...)` |
| `__init__` | Sets up initial attributes | `def __init__(self, name):` |
| Instance attribute | Data unique to an object | `self.name = name` |
| Class attribute | Data shared across all instances | `version = "1.0"` |
| Instance method | Function bound to an object | `def summary(self):` |
| Inheritance | Reuse/extend another class | `class Child(Parent):` |
| `super()` | Call the parent class's method | `super().__init__(...)` |
| Dunder method | Hook into built-in Python syntax | `__str__`, `__len__` |

---

## 9. Practice Ideas

- Write a `Dataset` class with `load()`, `clean()`, and `summary()` methods.
- Create a base `Model` class and two subclasses (`ClassificationModel`, `RegressionModel`) that override an `evaluate()` method.
- Add `__len__` and `__str__` to a custom class and test them with `len()` and `print()`.
- Refactor one of your earlier function-based exercises (e.g., `train_model()` from `functions.md`) into a class-based `TrainingPipeline`.
