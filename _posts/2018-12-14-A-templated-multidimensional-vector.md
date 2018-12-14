---
layout: post
title: A templated multidimensional vector
categories: [general,programming,cpp,templates,learn]
fullview: false
comments: true
---

**Disclaimer**: as you might have read in my [presentation post]({{ site.baseurl }}{%link _posts/2018-11-29-Welcome.md%}) I'm not a C++ expert, I consider myself as a C++ programmer who is trying to increase its experience from the *academic* view  towards a *real-life* programming.  
I will share with the readers my findings, with all their errors and imprecisions; don't take what I write as correct, by any means, but rather feel free to scrutinize, check and correct me for any error! 
Posts that will fit in this category more than the other will be all put inside the [learn](https://bznein.github.io/categories.html#learn-ref) category.  
<br>
<br>

All the code in this post is available on my repository [here](https://github.com/bznein/intent).


# The problem
Every once in a while, both for work and in the programming I do in my free time, I find the need to represent a grid indexed by *integer values* (e.g. a *vector*, or a *matrix* with integer indices).  

Most of the time I either need a 1-dimensional or a 2-dimensional grid, whose implementation is pretty straightforward.

{% highlight cpp %}
/* A one-dimensional grid can be just represented as a vector */
template <typename T>
std::vector<T> grid;

/* A two-dimensional grid can be just represented as a vector of vectors */
template <typename T>
std::vector<std::vector<T>> grid;
{% endhighlight %}

However, things become messy and ugly to see when we go to higher dimensions (and believe me, it happens):


{% highlight cpp %}
/* If we want to go to 5 dimensions, it will look a bit like this */
template <typename T>
std::vector<std::vector<std::vector<std::vector<std::vector<T>>>>>;
{% endhighlight %}

Which is equally ugly to look as it is to write.
Of course we might think to write this mess just once and create a header file somewhere which definitions that might look like these:  

{% highlight cpp %}
template <typename T>
using grid1D = std::vector<T>;

template <typename T>
using grid2D = std::vector<std::vector<T>>;

template <typename T>
using grid3D = std::vector<std::vector<std::vector<T>>>;

template <typename T>
using grid4D = std::vector<std::vector<std::vector<std::vector<T>>>>;

template <typename T>
using grid5D = std::vector<std::vector<std::vector<std::vector<std::vector<T>>>>>;
{% endhighlight %}


This looks equally awful, but at least it can buried somewhere in a header file where nobody will (hopefully) ever need to go.

<br>
<br>
### At this point, two questions should arise in your head.
<br>
1. ***Can't I just use the fact that each time we increase the dimension we are just replacing T with a vector&lt;T&gt;?***
2. ***So if I need to go to 20 dimensions I would have to have 5x that ugly mess?***
<br>

<br>
Luckily for us, the answers are *Yes* to the first question and *Absolutely not* for the second!
Let's just start from question number 1 (number 2 will come for free!) with a simple observation:


{% highlight cpp %}
template <typename T>
using grid1D = std::vector<T>;

template <typename T>
using grid2D = std::vector<std::vector<T>>;

template <typename T>
using grid3D = std::vector<std::vector<std::vector<T>>>;
{% endhighlight %}

is equal to writing

{% highlight c++ %}
template <typename T>
using grid1D = std::vector<T>>;

template <typename T>
using grid2D = grid1<std::vector<T>>;

template <typename T>
using grid3D = grid2D<std::vector<T>>;
{% endhighlight %}

and this makes everything easier to read; but we still have the problem that we have to explicitly define every dimension that we need.  
The next step is to employ this recursive definition to obtain a templated, generic and extensible compile-time definition of our type!

We will define a **struct multidimensional_vector** to hold our type information, which will be defined recursively based on the number of dimensions



{% highlight cpp %}
template <size_t dimcount, typename T>
struct multidimensional_vector
 {
         typedef std::vector<typename multidimensional_vector<dimcount-1, T>::type > vec;
 };
{% endhighlight %}

Here we are creating a new type, called **type**, which is that of a *std::vector* whose template argument is the type of a *multidimensional_vector* with one less dimension.

As in any other recursion, we need a base to stop at: in this case, we say that a *multidimensional_vector* of dimension *0* and type *T* is just of type *T* (i.e., a vector of *int* in 0 dimensions is just an *int*).

{% highlight cpp %}
template<typename T>
struct multidimensional_vector<0,T> 
{
        typedef T type;
};
{% endhighlight %}

  
  <br>

To better understand what is going on, let's try to **very** informally to resolve this substitutions for **dimcount=3** and **T=int** (we are dropping the *std* namespace qualifier for brevity):


{% highlight cpp %}
multidimensional_vector<3,int>::type v;

/* Applying the definition of multidimensional_vector with dimcount=3 leads to */
vector<multidimensional_vector<2,int>::type>::type v;

/* Recursively: */
vector<vector<multidimensional_vector<1,int>::type>::type>::type v;

/* And: */
vector<vector<vector<multidimensional_vector<0,int>::type>::type>::type>::type v;

/* Finally we reach the base of the recursion, and for brevity we omit the substitutions of ::type */
vector<vector<vector<int>>> v;
{% endhighlight %}

At this point, our 3-dimensional vector of integers is ready to be used! And if we need to change the dimension, or the type contained, we just have to change one small part in the line in which we define **v**.

<br>
(Side note: if you are wondering why we stop at **dimcount=0** and type **T** instead of **dimcount=1** and type **std::vector&lt;T&gt;** I have two answers: first, I am a bit OCD and stopping the recursion at 1 didn't feel right; second, this makes easier changing the type from **std::vector** to something else)

<br>
<br>

#### What is missing?
There are a lot of things that can be added:
* In the next post I will talk about creating an equally generic function to default-initialize the generic multidimensional vector to a given value.
* The template substitution will not work if we give a *negative* number of dimensions, as we will get an infinite loop on template instantiation (**gcc** will complain with: *fatal error: template instantiation depth exceeds maximum of 900*). Since a negative number of dimensions make no sense in this case, we should add a check to prevent it.

##### Comments? Questions?
Please, feel free to comment either down below or by reaching out to me in private if you have questions, doubts or if you just have to point out errors, imprecisions and such. I am well aware that there are, probably, much better and cleaner ways to do this, I would love to see you pointing them out to me!  
As I said in the disclaimer, I see this series of posts as a way to grow as a C++ programmer together with the audience, not as a way to teach anyone anything. I'm positive that there are far better and cleaner solutions, and I'm eager to learn about them!


