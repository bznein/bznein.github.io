---
layout: post
title: I survived the Advent Of Code 2018"
categories: [general,programming,challenges]
fullview: false
comments: true
---

As promised in [last Monday's post](https://bznein.github.io/general/programming/challenges/2019/07/22/I'm-Not-Dead.html), I want to share you the experience of finally completing, for the first time in my life, a full [Advent of Code](https://adventofcode.com/) challenge.

All the code I will show (except when noted) will be taken from my [Github Repository](https://github.com/bznein/AoC2018); I wonàt repost here the full texts of the challenges, which can get quite verbose, and all the "lore" (Christmas-themes) around it, but I will just summarize the definition of the problems and the expected output.

Especially in these first few posts, I will discuss multiple days in a single article, given that some of the challenges are really easy and require no more than a few lines of code.

Last side note: as I said in the [presentation post](https://bznein.github.io/general/programming/challenges/2018/12/03/Advent-of-code-2018.html) about the challenge, each one is divided into two parts. I will give the link to each day when discussing it, but unfortunately you won't be able to see the second part of the challenge without solving the first one.

## So, let's start!
### [Day 1](https://adventofcode.com/2018/day/1)
This is a really simple warm-up day (the fastes solver took just ***26 SECONDS*** to complete the first part!) which we can summarize as follows:

#### *Part 1: Given a list of integer numbers, compute the total sum*

It is worth mentioning that in most cases, including this one, I will consider the input to be a file text in which each entry is on a different line.

In this case, a naive solution to the problem can be implemented as follows (omitting header inclusions for brevity):

{% highlight cpp %}
int main()
{
  auto ifS = std::ifstream("input.txt");
  int totFrequency=0, tmp;

  while(ifS >> tmp)
    totFrequency+=tmp;

  std::cout << totFrequency << std::endl;
}
{% endhighlight %}

This solution is straightforward, optimal in complexity (we are just reading every element once, you can't realyl get better than that!) and does the work just fine.

#### *Part 2: Find out the first partial sum that occurs twice* 
Let's give an example: suppose we have an input like this:
```
 3
-2
 4
-2
 6
```

The partial sums are the following:
```
 +3 = **3**
 +3 -2 = 1
 +3 -2 + 4 = 5
 +3 -2 + 4 -2 = **3**
 +3 -2 + 4 -2 +6 = 9
  ```
  
  We see that get as partial sum **3** two times, and it is the first value for which this happens, and that makes **3** the output to our problem.
  
  But there's a caveat to this! If no partial sum occurs twice in our list, we have to loop from the beginning of our list again until we find one!
