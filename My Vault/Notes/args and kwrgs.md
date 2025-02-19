
# args and kwrgs


DATE:  19-02-25


Tags: [[python]]

# References:




# Content:

In Python, `*args` and `**kwargs` are used to pass a variable number of arguments to a function. They allow you to write more flexible and reusable functions by accepting an arbitrary number of positional and keyword arguments.

### 1. `*args` (Arbitrary Positional Arguments)

- `*args` is used to pass a variable number of **non-keyword (positional) arguments** to a function.
- The `*` before `args` collects all the positional arguments into a **tuple**.
- You can name it anything (e.g., `*numbers`), but `*args` is the conventional name.

#### Example:
```
def my_function(*args):
    for arg in args:
        print(arg)

my_function(1, 2, 3)
```
**Output:**
```
1
2
3
```
Here, `args` is a tuple containing `(1, 2, 3)`.

---

### 2. `**kwargs` (Arbitrary Keyword Arguments)

- `**kwargs` is used to pass a variable number of **keyword arguments** (i.e., key-value pairs) to a function.
- The `**` before `kwargs` collects all the keyword arguments into a **dictionary**.
- You can name it anything (e.g., `**options`), but `**kwargs` is the conventional name.

#### Example:
```
def my_function(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

my_function(name="Alice", age=30, city="New York")

```
**Output:**

```
name: Alice
age: 30
city: New York
```
Here, `kwargs` is a dictionary containing `{"name": "Alice", "age": 30, "city": "New York"}`.

---

### Combining `*args` and `**kwargs`

You can use both `*args` and `**kwargs` in the same function to accept both positional and keyword arguments.

#### Example:
```
def my_function(*args, **kwargs):
    print("Positional arguments:", args)
    print("Keyword arguments:", kwargs)

my_function(1, 2, 3, name="Alice", age=30)

```
**Output:**
```
Positional arguments: (1, 2, 3)
Keyword arguments: {'name': 'Alice', 'age': 30}
```
---

### Rules for Using `*args` and `**kwargs`

1. `*args` must come before `**kwargs` in the function definition.
2. You can use other parameters alongside `*args` and `**kwargs`, but the order must be:
    - Standard positional arguments
    - `*args`
    - Standard keyword arguments
    - `**kwargs`

#### Example:
```
def my_function(a, b, *args, c=10, **kwargs):
    print("a:", a)
    print("b:", b)
    print("args:", args)
    print("c:", c)
    print("kwargs:", kwargs)

my_function(1, 2, 3, 4, c=20, name="Alice", age=30)
```
**Output:**
```
a: 1
b: 2
args: (3, 4)
c: 20
kwargs: {'name': 'Alice', 'age': 30}
```
---

### Practical Use Cases

- **Flexible APIs**: When you want to create functions that can handle a variety of inputs.
- **Decorators**: Commonly used in decorators to pass arguments to wrapped functions.
- **Inheritance**: When overriding methods in subclasses, `*args` and `**kwargs` can be used to pass arguments to the parent class method.

By using `*args` and `**kwargs`, you can write more dynamic and adaptable functions in Python.



