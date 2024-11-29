
# raise


DATE:  27-11-24


Tags: [[python]]

# References:




# Content:

The `raise` command is used to deliberately trigger (or "throw") an exception in your code. It's like sending up a signal that something unexpected or error-like has happened, which allows you to handle that situation in a controlled manner.

Let's break this down with some progressively more complex examples:

### 1. Basic Exception Raising
```
def divide_numbers(a, b):     
	if b == 0:        
		raise ValueError("Cannot divide by zero!")    
	return a / b 

```
#### This will work normally 
```
result = divide_numbers(10, 2)  # Returns 5.0 
```
#### This will raise an exception 
```
try:     
	result = divide_numbers(10, 0) 
except ValueError as e:     
	print(e)  # Prints: "Cannot divide by zero!"`
```
In this example, `raise` creates a `ValueError` with a custom message when someone tries to divide by zero. Instead of letting the program crash, we can catch and handle this exception.



### 2. Propagating Exceptions
```
def validate_age(age):     
	if age < 0:        
		raise ValueError("Age cannot be negative")    
	if age > 120:        
		raise ValueError("Age seems unrealistically high")    
	return age 
	
def process_person(name, age):     
	try:        
		valid_age = validate_age(age)        
		print(f"{name} is {valid_age} years old")    
	except ValueError as e:        
		print(f"Error processing {name}: {e}")`
```
Here, `raise` is used to signal specific validation problems. The exception "bubbles up" to the calling function, which can then decide how to handle it.

### 3. Re-raising Exceptions


```
def complex_operation():     
	try:        # Some operation that might fail        
		result = risky_calculation()    
	except Exception as e:               
		print(f"An error occurred: {e}")  # Log the error                 
		raise  # Re-raise the same exception to be handled by caller
```
The `raise` without an argument re-raises the most recently caught exception. This is useful when you want to log an error but still want the caller to handle the full exception.

### 4. Custom Exceptions

```
class InsufficientFundsError(Exception):     
	"""Custom exception for banking scenarios"""    
	def __init__(self, balance, amount):        
		self.balance = balance        
		self.amount = amount        
		super().__init__(f"Insufficient funds. Balance: ${balance}, Requested: ${amount}") 
		
def withdraw(balance, amount):     
	if amount > balance:        
		raise InsufficientFundsError(balance, amount)    
	return balance - amount 
	
try:     
	new_balance = withdraw(100, 150) 
except InsufficientFundsError as e:     
	print(f"Transaction failed: {e}")
```

By creating custom exceptions, you can make your error handling more specific and informative.

### Key Things to Understand About `raise`:

1. It interrupts the normal flow of the program
2. It can be caught by `try`/`except` blocks
3. If not caught, it will cause the program to terminate
4. You can raise built-in exceptions or create custom ones
5. You can include a custom error message

### Mental Model: Raising Exceptions

Think of `raise` like a fire alarm in a building:

- When something goes seriously wrong, you pull the alarm
- The alarm (exception) alerts everyone (code)
- People (code) can then respond appropriately
- If no one responds, the entire building (program) evacuates (crashes)

### When to Use `raise`:

- Validating input
- Handling impossible scenarios
- Signaling that something unexpected happened
- Providing more context about errors

Would you like me to elaborate on any part of this explanation? Do you see how `raise` helps you control and communicate errors in your code?




