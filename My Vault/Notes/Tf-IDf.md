
# Tf-IDf


DATE:  10-07-24


Tags:

# References:

https://www.youtube.com/watch?v=ruBm9WywevM&list=WL&index=80&t=583s


# Content:

Tf(q, D): Term Freq; func of query and document

IDf(q):  func of query;  Uniqueness of this query among all documents, makes sure that stop words like a and very common words don't get a really high score


### Issues with Tf-IDf
- If we have a query "cat", ![[Attachments/Pasted image 20240710133202.png]]Under TF IDF we would basically just say document B is a far better match to your query than document A because your query the word cat appears 10 times instead of just 1 time. But considering the length, 1/10=10% > 10/1000=1%, "cat" occurs more frequently in A. But Tf-IDf would return Doc B and not A. So there needs to be a penalty on length.
- Partial derivative of Tf-IDf wrt Tf is not a func of Tf:![[Attachments/Pasted image 20240710133751.png]]So increasing Tf does not change Tf only changes IDf.  
  Let's say we go from 1 to 2 instance of term "cat". That should be a pretty monumental leap I'm matching two times instead of one time, basically doubling the amount of matches that I have in a document. Should give me a big boost in overall score. But if I have 100 occurrences and again I have the same incremental increase going from 100 to 101, does that really make that document the same amount more relevant than it was going from the 1 to 2 case? So  there needs to be some kind of marginal returns to scale here. So if you have very few occurrences then every extra occurrence should get lots of points but if you already have tons of occurrences then increase in points should be lesser.
 
