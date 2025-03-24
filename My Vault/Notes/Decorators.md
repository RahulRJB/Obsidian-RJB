
# Decorators


DATE:  07-03-25


Tags:  [[Notes/python|python]]

# References:
https://www.youtube.com/watch?v=FsAPt_9Bf3U&list=WL&index=84



# Content:

Decorators are a powerful feature in Python that allow you to modify or extend the behavior of functions or methods without changing their actual code. They use the `@` syntax and are applied above function definitions.
























## Basic Decorator Concept

A decorator is essentially a function that takes another function as an argument, adds some functionality, and returns the modified function. Here's a simple example:


```
def my_decorator(func):     
	def wrapper():        
		print("Something is happening before the function is called.")
		func()        
		print("Something is happening after the function is called.")    
	return wrapper 
	
@my_decorator 
def say_hello():     
	print("Hello!")
```
Output:
```
Something is happening before the function is called. 
Hello! 
Something is happening after the function is called.
```

## Common Uses for Decorators

### 1. Function Timing and Profiling

python

Copy

`import time def timing_decorator(func):     def wrapper(*args, **kwargs):        start_time = time.time()        result = func(*args, **kwargs)        end_time = time.time()        print(f"{func.__name__} took {end_time - start_time:.6f} seconds to run")        return result    return wrapper @timing_decorator def slow_function():     time.sleep(1)    return "Function completed"`

### 2. Logging

python

Copy

`def log_function_call(func):     def wrapper(*args, **kwargs):        print(f"Calling {func.__name__} with args: {args}, kwargs: {kwargs}")        result = func(*args, **kwargs)        print(f"{func.__name__} returned: {result}")        return result    return wrapper @log_function_call def add(a, b):     return a + b`

### 3. Input Validation

python

Copy

`def validate_inputs(func):     def wrapper(*args, **kwargs):        for arg in args:            if not isinstance(arg, (int, float)):                raise TypeError("All arguments must be numbers")        return func(*args, **kwargs)    return wrapper @validate_inputs def calculate_average(a, b, c):     return (a + b + c) / 3`

### 4. Caching/Memoization

python

Copy

`def memoize(func):     cache = {}    def wrapper(*args):        if args in cache:            return cache[args]        result = func(*args)        cache[args] = result        return result    return wrapper @memoize def fibonacci(n):     if n <= 1:        return n    return fibonacci(n-1) + fibonacci(n-2)`

### 5. Authentication and Access Control

python

Copy

`def require_auth(func):     def wrapper(*args, **kwargs):        if not check_user_authenticated():  # Implement this function as needed            raise PermissionError("Authentication required")        return func(*args, **kwargs)    return wrapper @require_auth def view_sensitive_data():     return "This is sensitive data"`

## Advanced Decorators

### Decorators with Arguments

python

Copy

`def repeat(n):     def decorator(func):        def wrapper(*args, **kwargs):            results = []            for _ in range(n):                results.append(func(*args, **kwargs))            return results        return wrapper    return decorator @repeat(3) def say_hi():     return "Hi!"`

### Class Decorators

python

Copy

`class CountCalls:     def __init__(self, func):        self.func = func        self.count = 0             def __call__(self, *args, **kwargs):        self.count += 1        print(f"{self.func.__name__} has been called {self.count} times")        return self.func(*args, **kwargs) @CountCalls def hello():     print("Hello world!")`

### Method Decorators

python

Copy

`def check_permission(permission):     def decorator(method):        def wrapper(self, *args, **kwargs):            if not hasattr(self, 'permissions') or permission not in self.permissions:                raise PermissionError(f"Missing required permission: {permission}")            return method(self, *args, **kwargs)        return wrapper    return decorator class User:     def __init__(self, name, permissions=None):        self.name = name        self.permissions = permissions or []         @check_permission("admin")    def delete_user(self, user_id):        print(f"Deleting user {user_id}")`

## Best Practices

1. **Use `functools.wraps`**: This preserves the original function's metadata.

python

Copy

`from functools import wraps def my_decorator(func):     @wraps(func)  # Preserves func's metadata    def wrapper(*args, **kwargs):        return func(*args, **kwargs)    return wrapper`

2. **Handle `*args` and `**kwargs`**: Makes your decorators more flexible.
3. **Keep decorators focused**: Each decorator should have a single responsibility.
4. **Document decorator behavior**: Use docstrings to explain what your decorator does.

Decorators are particularly useful for implementing cross-cutting concerns like logging, authentication, and performance monitoring without cluttering your business logic. They embody the principle of separation of concerns and help keep your code DRY (Don't Repeat Yourself).



