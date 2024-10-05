
# Collaborative Filtering


DATE:  06-10-24


Tags:

# References:

https://www.youtube.com/watch?v=Fmtorg_dmM0


# Content:

- Key Idea: Past similar preferences can inform future preferences.
- On a site like Netflix, there are many other users. They all have the ability to rate the different pieces of content from 1 to 5. Now if i have a couple users that have given more or less the same rating to the same piece of content then They can be considered as similar, i.e they have similar tastes similar preferences. Now if i have one of those users come along and i need to know what to recommend to them next, i might go visit the similar users and see what those people have liked in the past and recommend this person one of those pieces of content.
- #### Simple Example:
	- 3 users, 8 shows:![[Attachments/Pasted image 20241006015353.png]]Shows 4&5 have avg rating of 3.5 by users 2 and 3. Show 6 unwatched, so we do not consider it. Show what should we recommend User1, show4 or show5?
- For this we need to check, which user more similar to User 1, user 2 or 3?![[Attachments/Pasted image 20241006015819.png]]Obviously User2 seems much more similar than User3. To quantitatively put this we use cosine similarity metric. The ratings of a user is considered as its vector.![[Attachments/Pasted image 20241006020305.png]]
- Now if we try to predict the rating of user1 for shows 4 & 5, we calculate using weighted avg:![[Attachments/Pasted image 20241006020521.png]]
- Barriers:
	- Sparsity: Much more content and most users dont rate anything
	- Scalability: Much complex with many users and many shows





