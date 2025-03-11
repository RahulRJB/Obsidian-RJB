
# Class


DATE:  12-03-25


Tags: [[Notes/python|python]]

# References:



# Content:

## 1. Classes and Instances:
https://www.youtube.com/watch?v=ZDa-Z5JzLYM&list=PL-osiE80TeTsqhIuOqKhwlXsIBIdSeYtc&index=1


```
class Employee:

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@email.com'
        self.pay = pay

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'Employee', 60000)

emp_1.fullname() == Employee.fullname(emp_1)  # the instance is passed automatically as the first variable

```
## 2. Class Variable:
https://www.youtube.com/watch?v=BJ-VvGyQxho&list=PL-osiE80TeTsqhIuOqKhwlXsIBIdSeYtc&index=2

Class variables are variables shared among all instances of a class1 . They contain data that should be the same for each instance.

Here's a breakdown of how class variables differ from instance variables, according to the source:
- Instance variables hold data unique to each instance1 . They are defined within methods using the self argument, like names, email, or pay in an Employee class.
- Class variables are defined outside of methods, usually at the top of the class.
- Accessing class variables: Class variables are accessed using the class name itself (e.g., Employee.raise_amount) or through an instance of the class (e.g., employee_1.raise_amount)3 . When trying to access an attribute from an instance, Python first checks if the instance contains the attribute. If not, it checks the class or any inherited classes.
- Modifying class variables: Changing a class variable using the class name (e.g., Employee.raise_amount = 1.05) affects all instances5 . Modifying it through an instance (e.g., employee_1.raise_amount = 1.05) creates a new instance variable specific to that instance, shadowing the class variable.

The source uses the example of a company-wide annual raise to illustrate the use of a class variable2 . The raise amount would be the same for all employees, making it suitable as a class variable2 . The source also uses the example of tracking the number of employees as another use case for class variables, where it wouldn't make sense to have this number vary by instance

![[Attachments/Pasted image 20250312025821.png]]

raise_amount is in the namespace of class and not of the instance here:
![[Attachments/Pasted image 20250312025923.png]]![[Attachments/Pasted image 20250312030015.png]]
Update to namespace of the instance variable:
![[Attachments/Pasted image 20250312025646.png]]


## 3. Class and Static Methods:

https://www.youtube.com/watch?v=rq8cL2XMM5M&list=PL-osiE80TeTsqhIuOqKhwlXsIBIdSeYtc&index=3

**RegularMethod**: pass instance as the first argument
**ClassMethod**: pass class as the first argument
**StaticMethod**: pass nothing - behave like regular function, but we include them because they have some logical connection with our class
```
class Employee:

    num_of_emps = 0
    raise_amt = 1.04

    def __init__(self, first, last, pay):
        self.first = first
        self.last = last
        self.email = first + '.' + last + '@email.com'
        self.pay = pay

        Employee.num_of_emps += 1

    def fullname(self):
        return '{} {}'.format(self.first, self.last)

    def apply_raise(self):
        self.pay = int(self.pay * self.raise_amt)

    @classmethod
    def set_raise_amt(cls, amount):
        cls.raise_amt = amount

    @classmethod
    def from_string(cls, emp_str):
        first, last, pay = emp_str.split('-')
        return cls(first, last, pay)

    @staticmethod
    def is_workday(day):
        if day.weekday() == 5 or day.weekday() == 6:
            return False
        return True


emp_1 = Employee('Corey', 'Schafer', 50000)
emp_2 = Employee('Test', 'Employee', 60000)

Employee.set_raise_amt(1.05)

print(Employee.raise_amt)
print(emp_1.raise_amt)
print(emp_2.raise_amt)

emp_str_1 = 'John-Doe-70000'
emp_str_2 = 'Steve-Smith-30000'
emp_str_3 = 'Jane-Doe-90000'

first, last, pay = emp_str_1.split('-')

#new_emp_1 = Employee(first, last, pay)
new_emp_1 = Employee.from_string(emp_str_1)

print(new_emp_1.email)
print(new_emp_1.pay)

import datetime
my_date = datetime.date(2016, 7, 11)

print(Employee.is_workday(my_date))
```