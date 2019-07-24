---
layout: post
title: I survived the Advent Of Code 2018
categories: [general,programming,challenges]
fullview: false
comments: true
---

As promised in [last Monday's post](https://bznein.github.io/general/programming/challenges/2019/07/22/I'm-Not-Dead.html), I want to share you the experience of finally completing, for the first time in my life, a full [Advent of Code](https://adventofcode.com/) challenge.

All the code I will show (except when noted) will be taken from my [Github Repository](https://github.com/bznein/AoC2018); I wonàt repost here the full texts of the challenges, which can get quite verbose, and all the "lore" (Christmas-themes) around it, but I will just summarize the definition of the problems and the expected output.

Especially in these first few posts, I will discuss multiple days in a single article, given that some of the challenges are really easy and require no more than a few lines of code.

Last side note: as I said in the [presentation post](https://bznein.github.io/general/programming/challenges/2018/12/03/Advent-of-code-2018.html) about the challenge, each one is divided into two parts. I will give the link to each day when discussing it, but unfortunately you won't be able to see the second part of the challenge without solving the first one.

(side note: except when noted, I will consider the input to be given inside a single file called *input.txt*
## So, let's start!
### [Day 1](https://adventofcode.com/2018/day/1)
This is a really simple warm-up day (the fastes solver took just ***26 SECONDS*** to complete the first part!) which we can summarize as follows:
<details>
  <summary>Part 1: Given a list of integer numbers, compute the total sum
  </summary>

The straightforward, naive implementation is the one I used for the challenge and it can be seen [here](https://github.com/bznein/AoC2018/blob/master/Day01/part1/main.cpp). However, here I would loke to show a more elaborate one, that doesn't use manually written for loops and relies instead on built-in mechanism such as *[istream_iterator](https://en.cppreference.com/w/cpp/iterator/istream_iterator)* and *[std::accumulate](https://en.cppreference.com/w/cpp/algorithm/accumulate)*

{% highlight cpp %}
int main()
{
    auto ifs = std::ifstream("input.txt");
    auto ifsIt=std::istream_iterator<int>(ifs); // istream_iterator to the input file. Conversion from string to int specified in the template type 
    std::istream_iterator<int> eos; // Represents the "end" iterator

    std::cout << std::accumulate(ifsIt, eos,0); // Loop between the iterators and accumulate the result (initialized at 0)
}
{% endhighlight %}
</details>

<details>
  <summary>Part 2: Find out the first partial sum that occurs twice</summary>
  
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
  
  We see that get as partial sum **3** two times, and it is the first value for which this happens, and that makes **3** the output to our problem. The caveat is that we do not have the guarantee that a value will occur twice while scanning the list. In that case we are required to start from scratch and keep looping until we found an answer.
  
  The solution is not particulary interesting and not worth to be posted here, you can read it [on github](https://github.com/bznein/AoC2018/blob/master/Day01/part2/main.cpp). Just note the presence of *[std::map](https://en.cppreference.com/w/cpp/container/map)*, as it will become the most used type in the whole challenge!
  </details>
    
  
  
  
### [Day 2](https://adventofcode.com/2018/day/2)
Day 2 make us work with strings: specifically with single characters inside them.


<details>
  <summary>Part 1: count the amount of string containing exactly two/three instances of a single character, then multiply them</summary>
  
  
  E.g.: the string *abcad* counts for one, since it contais two instances of *a*.
        the string *abcde* doesn't contribute to the total.
        the string *aabbbc* counts for two, containing both two *a* and three *b*.
        
  Multiplying them gives a final result of *2*.
  
  The solution is again pretty straightforward and relies on a simple function, called *exactOccurrences* that takes as input the string *s* and an integer number *i*, and returns true if there is at least a character in *s* that appears **exacly** *s* times.
  
  You can find the code [here](https://github.com/bznein/AoC2018/blob/master/Day02/part1/main.cpp) and once again it is not worth being posted or commented here. *exactOccurrences* is a simple for loop which makes us of *[std::count](https://en.cppreference.com/w/cpp/algorithm/count)* and the main function just loops through the input file and accumulates the return values of *exactOccurrences(s,2)* and *exactOccurrences(s,3)*.
</details>

<details>
  <summary>Part 2: find words with a difference of just one letter - and print common letters</summary>
  
  
  This is another pretty easy task: given a list of strings such as 
  ```
  abcde
fghij
klmno
pqrst
fguij
axcye
wvxyz
```

the two strings that satisfy the requirement (they are guaranteed to exist, and to be a unique pair) are *fghij* and *fguij*, which differ only for the character in third position. The final output of our program must be the common letters, so in our case *fgij*.

As usual, you can read my solution [here](https://github.com/bznein/AoC2018/blob/master/Day02/part2/main.cpp), and we are still in the realm of vary straightforward solves. 


Although this solution does the job, I'm still not 100% convinced by it, and I would like to hear feedback about it.
One obvious improvement that comes to my mind as I read this code, is that we can rewrite the two nested for loops from 


{% highlight cpp %}
for (int i=0; i<ss.size(); ++i)
    for (int j=0; j<ss.size(); ++j)
      if (i!=j && differByOne(ss[i],ss[j]))
      {
          printCommonLetters(ss[i],ss[j]);
          return 0;
      }
{% endhighlight %}

to

{% highlight cpp %}
for (int i=0; i<ss.size(); ++i)
    for (int j=i+1; j<ss.size(); ++j)
      if (differByOne(ss[i],ss[j]))
      {
          printCommonLetters(ss[i],ss[j]);
          return 0;
      }
{% endhighlight %}

which saves us from checking the same pair twice.

However, I think that there must be a better, maybe built-in way, to iterate through all possible pairs in a vector, but I wasn't able to find it. If you know something suited for this, please comment below!






