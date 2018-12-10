---
layout: post
title: Cpp Day 2018 (Part 2)
categories: [general,programming,cpp]
fullview: false
comments: true
---

Here's the second part of my report of the Cpp Day 2018 organized by the [Italian C++ Community](https://www.italiancpp.org/).
For the first part of the report look [here]({{ site.baseurl }}{%link _posts/2018-12-04-CppDay-2018.md%})

## <center>C++ Day 2018</center>
### <center>Pavia, November 24th</center>
The event was hosted at the local Department of Physics of the University of Pavia, completely free of charge!  
After a quick and efficient check-in (which included receiving a ticket for free lunch!) the audience moved into the main room for the C++ Day to begin!


### Summary:
* [**Morning talks**]({{ site.baseurl }}{%link _posts/2018-12-04-CppDay-2018.md%})
* [Unevaluated operands: the SFINAE you don't expect](#sfinae)
* [Lessons Learned Developing Evolutionary Algorithms in C++](#evolutionary-algorithms)
* [Macro free non intrusive runtime reflection system in C++](#reflection)
* [GIS in C++](#gis)
* [Closing Message](#closing-message)


### SFINAE
#### Unevaluated operands: the SFINAE you don't expect
##### [Michele Caini](https://www.italiancpp.org/speakers/#mcaini)
Until just a couple of months ago the letters **SFINAE** would have meant absolutely nothing for me. This misterious acronym might scare **C++** novices and, in general people who are not used to templates as much as they should (and I'm putting myself on this list as well).  
  
  In a short but powerful 20 minutes presentation, Michele guides us not only towards understanding the meaning of SFINAE (which, by the way, stands for **S**ubstitution **F**ailure **I**s **N**ot **A**n **E**rror), but also towards
  a nice example on how to use it for practical purposes. His presentation contains the right balance between theory (definitions) and code to be perfectly understandable to almost everyone!
    
      
  I highly suggest you to read the slides, maybe while trying to implement a similar, minimal example!
  
  
**[Click here for the slides](https://github.com/italiancpp/cppday18/blob/master/Unevaluated%20Operands%20-%20Michele%20Caini.pdf)** 


### Evolutionary Algorithms
#### Lessons Learned Developing Evolutionary Algorithms in C++
##### [Manlio Morini](https://www.italiancpp.org/speakers/#mmorini)
The topic shifts completely for the presentation of Manlio which, due to the sudden abscence of another speaker, becomes a plenary one.  

In 50 minutes, Manlio tries to guide us towards a general big picture of **A**rtificial **I**ntelligence and genetic algorithms, talkig about generating pseudo-random sequences of numbers in C++, problem states, fitness and others AI-related topuics.  

To be perfectly honest, I would say that Manlio's presentation was interesting in a general way, but missed to get to a precise point; he talked about a large number of things without actually going into details of any of these.

**[Click here for the slides](https://github.com/italiancpp/cppday18/blob/master/Lessons%20Learned%20Developing%20Evolutionary%20Algorithms%20in%20C%2B%2B%20-%20Manlio%20Morini.pdf)** 

### Reflection
#### Macro free non intrusive runtime reflection system in C++
##### [Michele Caini](https://www.italiancpp.org/speakers/#mcaini)
Time again for a presentation by Michele Caini. This time I had a hard time choosing which presentation to attend, since the other track was a nice wrap-up about the current status for C++20, by the great [Alberto Barbati](https://www.italiancpp.org/speakers/#abarbati).  
In the end I decided to trust again Michele, and although the topic of this 50 minutes talk was much harder for me, I don't regret the choice. Talking about the (lack of) reflection in C++, Michele gave an introduction about it, discussing the difference between static and dinamic reflection, pros and cons of both, and then dived into the presentation of a minimal subset of [Meta](https://github.com/skypjack/meta), his header-only reflection system for C++.  

Another good presentation by Michele, although 50 minutes are an incredibly short time for such a complex theme, and overall what an inexperienced listener like me might still miss at the end is the answer to the question *Why would I need reflection though?*.

I still suggest you to read his slides, and possibly to have a look at its [GitHub repository](https://github.com/skypjack/meta).

**[Click here for the slides](https://github.com/italiancpp/cppday18/blob/master/Runtime%20Reflection%20-%20Michele%20Caini.pdf)** 
  
### GIS
#### GIS in C++
##### [Alberto Bignotti](https://www.italiancpp.org/speakers/#abignotti)
The ending presentation for the **Cpp day** for me was about GIS, a topic I never worked on but which I always found interesting.  
Alberto gave a nice but a little *light* presentation, with a lot of demos, some scratch of code without going much into details..  
Normally I would not appreciate too much a similar approach, as I love to get dirty into the code, seeing even the smallest details to fully grasp it, but I have to say that to end the day, and after a though presentation like the one before, I did not have too much to complain about.  
Alberto gave us a bit of introduction to Geographical systems, refreshing what I knew from academy, like the concepts of Datum, ellipsoid, projections and so on.  
He also went a bit into the details of developing mobile GIS applications, how to handle projections and transformations, with a bit of math which was never too much wrt to the amount of code shown.
Overall, a light but interesting talk.


**[Click here for the slides](https://github.com/italiancpp/cppday18/blob/master/GISinCPP.pptx)** 
  
### Closing message
This wonderful **C++ Day** ended with a nice closing message by the organizers, giving special prizes to the *best* tweets (definition of *best* unspecified) and thanking everyone for the attendace.  
The organizers reminded everyone that they are still looking for a place for the **Cpp Day 2019** so, if you are interested, you are welcome to [get in touch](mailto:info@italiancpp.org) with them!  

I will probably not be there next year, as I am moving abroad from January, but I would like to take this opportunity to thank Marco and all of the staff at **++it** for their incredible work and everything they have created in these years!!
