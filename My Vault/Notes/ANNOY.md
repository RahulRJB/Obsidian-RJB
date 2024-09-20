

# ANNOY(Approximate Nearest Neighbors oh yea)


DATE:  20-09-24


Tags:  [[Clustering]] 

# References:

https://www.youtube.com/watch?v=DRbjpuqOsjk&list=WL&index=93


# Content:

## Issues with KNN:
- For a large dataset with millions of record and you want to find complexity for finding K NN to a given point, is O(N). As we double the datapoints, for each datapoint we need to find its distance from the given point 'X' so time also doubles, so O(N).
- Also dependent on dimensions and K.

## Goal of ANN:
- What if we got almost closest nearest neighbour. Sacrificing accuracy for speed.

## ANNOY:
- Designed by Spotify.
- ![[Attachments/Pasted image 20240920022119.png]]
	- We assume to just have 16 data points in 2-dimensional space. Ofcourse if you're using this you're going to have a lot of data, that's the whole reason you'd use it.
	- We start with picking 2 data points at random, let's say we pick 1 and 9. This is represented by this top layer of the tree. This tree structure is going to be crucial to understanding how this approximate nearest neighbor algorithm works.
	- The first node says that i picked 1 and 9 randomly, we then draw a line which is equidistant from those 2 points and that is this blue line here. This line effectively separates the space of points into two pieces.
	- ![[Attachments/Pasted image 20240920022235.png]]We adopt the convention that anytime we take the left branch of the tree, we are looking at stuff below the separating line, and anytime you take the right branch you're looking at stuff above the separating line.
	- We're going to keep splitting till a certain threshold, 
criteria we have and today's criteria is

going to be that

we need to keep splitting until we have

at most

three data points in each region and

currently this whole bottom part is one

region and we have five data points

there

so that's our signal to keep going so

let's say we

split again and now it just proceeds

just as we did before we pick another

two random data points within this

region

let's say we pick data point one and

data point four and then we draw the

equidistant line again

which is now this red line here if we

look below that red line we have data

points four

five and eight four five and eight and

we can stop separating stuff in that

region because we've

achieved this three data points or less

criteria

and we're going to call that region r1

so let me grab my marker and label it

on the diagram too so this becomes

region r1 and we're done

let's take a look at what happens above

the red line we've also met the criteria

there we just have two data points 1 and

13.

we're going to call that region r2 so

we're done with those we have these two

regions in space

and now we just keep going now there's

also stuff to do above the line i won't

go through all the details because i

think you start to get it how about we

just do one more to make sure you

understand it

so above the line we have all these data

points we pick two at random let's say

we pick data point six and data point

twelve

and so six and 12 that leads to this

purple separating line here

and so the region above 2 12 and 15

that's done

and we continue splitting down here so

at the end of the day

after we've done this whole method we

have the tree that you see on the right

hand side here

now the big burning question is why did

we just do this what's the point

seems like a cool thing to do but how

does this help us find the k closest

neighbors

approximately to some given point

it actually helps us a lot and more

importantly helps us do it in a very

efficient way![[Attachments/Pasted image 20240920022607.png]]



- ![[Attachments/Pasted image 20240920022814.png]]so let's do that by just showing you: a new data point that comes in

right here and i want to ask who is the

closest neighbor to me

if i was using original k nearest

neighbors i would have to basically just

scan through all 16 of these data points

compute the distances and i would find

out the correct answer is

maybe eight but how do i use this

structure to do that in a much more

efficient way

well i just traverse the tree so i start

at the top of the tree

and i ask is this new data point here

above or below

the blue line and it's below the blue

line so i travel down this side of the

tree

then i ask is this new data point above

or below the red line

and it's below the red line so i travel

below here and now i am in region one

and now here is the crux of the

approximate nearest neighbors algorithm

now that i'm in region 1 which consists

of only points 4 5 and 8

i only check the distances between my

mystery point

and these three points here and then i

find that the closest neighbor would be

eight

what we're doing intuitively is that

when we constructed this tree

we did it in a random fashion but we've

created these regions in space

such that points that are near each

other usually appear within the same

region so that all i have to do to

classify some kind of mystery point or

figure out who's the closest neighbor to

some kind of mystery point

is figure out which region it's in and

then only search for neighbors within

that region because that's going to be

some kind of localized pocket of space

and in some sense i should be safe to

only search that region

- ![[Attachments/Pasted image 20240920022917.png]]## Drawbacks

drawbacks here and why is this called

approximate

where the places that can find the not

correct answer

for example for example let's say i had

a data point

right here right below that orange line

now the true answer for who's your

closest neighbor is obviously six

but you can probably see what's going to

go wrong after we go down this tree

we're going to land in region five

and so we're only going to be

considering 9 11 and 16

and the closest neighbor of that would

be nine but you can also see why this is

not like

catastrophic usually if you have like

one million ten million hundred million

data points

usually in applications like that you're

okay with a little bit of error

you don't want it's okay if you don't

find the six as long as you find

something that's

close enough


- ### Considerations:
	- ![[Attachments/Pasted image 20240920023332.png]]
	- we've constructed here is basically a

binary search tree

we're just going to go down one route or

the other at every single node

and so we know we have a binary search

tree we're basically

cutting the size of the problem in half

approximately with every branch we go

down and

that kind of behavior leads us to a

bigger complexity of not

n as we had in k nearest neighbors but

log n

so we have gone from something that is

order of n in complexity

in terms of how many data points you

have to search through to something that

is order of log

n and just to make sure this login

behavior is clear for anyone every time

you go down a part of the tree you're

basically saying oop

throw away everything over here let's

search this part of the space then you

split again and say throw away

everything over here let's search this

half of the space

and so you're basically just cutting the

problem half half half half each time

until you land in whichever region

you're in and then you just have to do

some fixed

number of comparisons to figure out who

are my two or three whatever closest

neighbors

and that's why it's order log n so this

is the biggest thing it has going for it

let's think about one of the drawbacks

though on this page over here we only

constructed one tree and we could have

gotten really unlucky we could have

gotten pretty

bad splits since we did this fully

randomly and so something that the

approximate nearest neighbors

oh yeah algorithm does is that instead

we construct

instead we construct a forest

a forest so what that means is that we

don't just construct one tree we

construct a whole set of trees

similar to the spirit of random forests

being collections of decision trees

we construct several trees that look

like the tree we had before

but each tree is going to be a little

bit different because of the random

points we choose for splitting

and just as we had the logic with random

forests being a better version of

decision trees because we're averaging

over lots of different

combinations lots of different

possibilities for what could have

happened instead of just one

we have the same exact logic here where

maybe we don't find the best closest

neighbors with just one tree

but when we look at the result of the

best closest neighbors across

many trees which are similar but

different in slight ways

we're going to get better overall

performance of course one of the other

drawbacks would be that when we have a

forest of let's say 100

of these search trees you have to

obviously store them

somewhere so this approximate nearest

neighbors does require some additional

data storage

but if we're willing to accept that data

storage we get a huge

speed up in finding the neighbors which

are closest to me

and doing it in a way that usually finds

pretty good approximations



