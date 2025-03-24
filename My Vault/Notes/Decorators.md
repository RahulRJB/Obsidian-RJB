
# Decorators


DATE:  07-03-25


Tags:  [[Notes/python|python]]

# References:
https://www.youtube.com/watch?v=FsAPt_9Bf3U&list=WL&index=84



# Content:


[[Notes/Closures|Closures]]: Closure remembers our message variable even after the outer function has finished executing. ![[Attachments/Pasted image 20250324185910.png]]

## Basics:

 A Decorator is just a function that takes another function as an argument adds some kind of functionality and then returns another function. All of this without altering the source code of the original function that you passed in.

In closures, if instead of a variable, we pass in a function, we get a Decorator!

*pseudocode*:  ![[Attachments/Pasted image 20250324190907.png]]
*Example*: ![[Attachments/Pasted image 20250324191200.png]]

Why do this? 
	Decorating our functions allows us to easily add functionality to our existing functions by adding that functionality inside of our wrapper: ![[Attachments/Pasted image 20250324191414.png]]

 ![[Attachments/Pasted image 20250324192212.png]]This is how we usually see Decorators in Python. This is the same thing as setting our original function equal to that function wrapped within our decorator. @ syntax here would be the same thing as saying that I want my display function equal to this decorator function with our original fun function passed in i.e. `display = decorator_function(display)`.   So anytime I run `display()`, it will have the new functionality added on.


Would not work if the OG function took in some arguments:![[Attachments/Pasted image 20250324192957.png]]

To prevent this we use `*args` and `**kwargs`:![[Attachments/Pasted image 20250324193253.png]]


Class as Decorator: ![[Attachments/Pasted image 20250324193731.png]]

## Common Uses for Decorators

### 1. Function Timing and Profiling

	```
	def timing_decorator(func):
		import time
		def wrapper(*args, **kwargs):        
			start_time = time.time()        
			result = func(*args, **kwargs)        
			end_time = time.time()        
			print(f"{func.__name__} took {end_time - start_time:.6f} seconds to run")        
			return result    
		return wrapper 
		
	
	@timing_decorator 
	def slow_function():     
		time.sleep(1)    
		return "Function completed"
		
	```
--- 
 ![[Attachments/Pasted image 20250324195008.png]]

### 2. Logging

	```
	def log_function_call(func):     
		def wrapper(*args, **kwargs):        
			print(f"Calling {func.__name__} with args: {args}, kwargs: kwargs}") 
			result = func(*args, **kwargs)        
			print(f"{func.__name__} returned: {result}")        
			return result    
		return wrapper 
	
	@log_function_call 
	def add(a, b):     
		return a + b
		```
---
![[Attachments/Pasted image 20250324194501.png]]

### 3. Input Validation


	```
	def validate_inputs(func):     
		def wrapper(*args, **kwargs):        
			for arg in args:            
				if not isinstance(arg, (int, float)):
					raise TypeError("All arguments must be numbers")
			return func(*args, **kwargs)    
		return wrapper 
		
	@validate_inputs 
	def calculate_average(a, b, c):     
		return (a + b + c) / 3
	```

### 4. Caching/Memorization


	```
		def memoize(func):     
			cache = {}    
			def wrapper(*args):        
				if args in cache:            
					return cache[args]        
				result = func(*args)        
				cache[args] = result        
				return result    
			return wrapper 
		
		
		@memoize 
		def fibonacci(n):     
			if n <= 1:        
				return n    
			return fibonacci(n-1) + fibonacci(n-2)
	```

### 5. Authentication and Access Control


	```
		def require_auth(func):     
			def wrapper(*args, **kwargs):        
				if not check_user_authenticated():  #Implement as needed
					raise PermissionError("Authentication required")
				return func(*args, **kwargs)    
			return wrapper 
			
		@require_auth 
		def view_sensitive_data():     
			return "This is sensitive data"
	```

## Advanced Decorators

### Decorators with Arguments

	```
	def repeat(n):
		def decorator(func):        
			def wrapper(*args, **kwargs):            
				results = []            
				for _ in range(n):                
					results.append(func(*args, **kwargs))            
				return results        
			return wrapper    
		return decorator 
	
	
	@repeat(3) 
	def say_hi():     
		return "Hi!"
		
	```
---
![[Attachments/Pasted image 20250324203735.png]]
### Class Decorators

	```
		class CountCalls:     
			def __init__(self, func):        
				self.func = func        
				self.count = 0             
			
			def __call__(self, *args, **kwargs):        
				self.count += 1        
				print(f"{self.func.__name__} has been called {self.count} times")
				return self.func(*args, **kwargs) 
		
		
		@CountCalls 
		def hello():     
			print("Hello world!")
	```

### Method Decorators

	```
	def check_permission(permission):     
		def decorator(method):        
			def wrapper(self, *args, **kwargs):            
				if not hasattr(self, 'permissions') or permission not in self.permissions:
					raise PermissionError(f"Missing required permission: {permission}")
				return method(self, *args, **kwargs)        
			return wrapper    
		return decorator 
		
	class User:     
		def __init__(self, name, permissions=None):
			self.name = name        
			self.permissions = permissions or []
			
	 
	 @check_permission("admin")    
	 def delete_user(self, user_id):        
		 print(f"Deleting user {user_id}")
		 
	 ```


### Creating a @retry decorator:

```
import time
import functools

def retry(max_attempts=3, delay_seconds=1, allowed_exceptions=(Exception,)):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            attempts = 0
            while attempts < max_attempts:
                try:
                    return func(*args, **kwargs)
                except allowed_exceptions as e:
                    attempts += 1
                    if attempts == max_attempts:
                        raise e
                    time.sleep(delay_seconds)
                    print(f"Retry {attempts}/{max_attempts} after error: {e}")
            return None  # This line should never be reached
        return wrapper
    return decorator

# Usage
@retry(max_attempts=5, delay_seconds=2, allowed_exceptions=(ConnectionError, TimeoutError))
def fetch_data():
    # Simulate an unstable API call
    import random
    if random.random() < 0.8:
        raise ConnectionError("Failed to connect")
    return "Data received"
```


## Best Practices

1. **Use `functools.wraps`**: This preserves the original function's metadata. This is useful when stacking the decorators! 

	```
	from functools import wraps 
	
	def my_decorator(func):     
		@wraps(func)  # Preserves func's metadata    
		def wrapper(*args, **kwargs):        
			return func(*args, **kwargs)    
		return wrapper
		
	```
Watch this video: https://www.youtube.com/watch?v=FsAPt_9Bf3U&list=WL&index=84
Timestamp:  20:00mins to end



2. **Handle `*args` and `**kwargs`**: Makes your decorators more flexible.
3. **Keep decorators focused**: Each decorator should have a single responsibility.
4. **Document decorator behavior**: Use docstrings to explain what your decorator does.

Decorators are particularly useful for implementing cross-cutting concerns like logging, authentication, and performance monitoring without cluttering your business logic. They embody the principle of separation of concerns and help keep your code **DRY (Don't Repeat Yourself).**



