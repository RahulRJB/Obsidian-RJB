
# Generators


DATE:  20-03-25


Tags: [[Notes/python|python]]

# References:




# Content:


 - Generators don't hold the entire result in memory,  it yields one result at a time.
 - ![[Attachments/Pasted image 20250320024812.png]]OR![[Attachments/Pasted image 20250320025016.png]]
 - Geneartor in the form of list comprehension:![[Attachments/Pasted image 20250320025212.png]]
 - PERFORMANCE: Generator is not holding all the values in memory. If we had millions of items to loop through then having that many items in memory will definitely be noticeable but with generators that's not the case. So whenever you cast a generator to a list, if this generator had a lot of values that it needed to convert to that list then you lose that performance in terms of it would put all of those values into memory.


```
import mem_profile
import random
import time

names = ['John', 'Corey', 'Adam', 'Steve', 'Rick', 'Thomas']
majors = ['Math', 'Engineering', 'CompSci', 'Arts', 'Business']

print 'Memory (Before): {}Mb'.format(mem_profile.memory_usage_psutil())

def people_list(num_people):
    result = []
    for i in xrange(num_people):
        person = {
                    'id': i,
                    'name': random.choice(names),
                    'major': random.choice(majors)
                }
        result.append(person)
    return result

def people_generator(num_people):
    for i in xrange(num_people):
        person = {
                    'id': i,
                    'name': random.choice(names),
                    'major': random.choice(majors)
                }
        yield person

# t1 = time.clock()
# people = people_list(1000000)
# t2 = time.clock()

t1 = time.clock()
people = people_generator(1000000)
t2 = time.clock()

print 'Memory (After) : {}Mb'.format(mem_profile.memory_usage_psutil())
print 'Took {} Seconds'.format(t2-t1)
```



