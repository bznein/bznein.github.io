layout: post
title: About some Range coolness...
categories: [general,programming,C++, C++20]
fullview: false
comments: true

------



*Small note: the posts about Advent Of Code are temporarily suspended, I need to devise a better way to present them, as they would have become boring and useless quite fast... I will probably focus just on two or three really interesting challenges and skip all the rest*.



Today, I will tackle a bit some of the things that make the **Ranges** library so damn cool!

## Readability

I said this before, and I will say it again: *if used properly, Ranges make code much more readable!*

The intended action is clearer, the pipe syntax will jump to the eyes of anyone who ever used bash, and a lot of bloated code can be made so much simpler!

An example? A task as basic as sorting a vector!

```c++
vector<int> v {5,6,2,1};

std::sort(v.begin(),v.end());
```

Sure, this is only two lines of code, and we can't do much better with Ranges:

```c++
vector<int> v {5,6,2,1};

v |= ranges::v3::action::sort;
```

but I dare you to say this doesn't look so simpler and clearer (and if you are really annoyed by nested namespace specifier, that can be fixed too, of course).



But ok, let's suppose that you are still not convinced by this example.

Fine, what about sorting a vector that is returned by a function? Can we do better than this?

```c++
auto v = functionReturningAVector();

std::sort(v.begin(),v.end());
```

Sure we can!

```c++
auto v = functionReturningAVector() | ranges::v3::action::sort;
```

There are so many other ways to improve readability with ranges that I won't have time to discuss here, such as chaining of multiple transformations. 

They will likely be the subject of a future post, though!



## Lazy evaluation

Lazy evaluation is a topic that too rarely comes to mind when discussing about C++.

I won't go here into details about lazy evaluation is, how it works, and why is it important (I'll instead leave you the [Wikipedia Article](https://en.wikipedia.org/wiki/Lazy_evaluation) which, while being a bit tedious, is well documented), but I will discuss about how **Ranges** bring lazy evaluation into the game!



Broadly speaking, we can distinguish between *views*, who perform lazy evaluation, and *actions*, who instead perform eager evaluation (Ranges official documentation talks about *lazy adaptation* and *eager modification*).

### Views

Range Views are *lazy*, *pure functional* adaptors, which means that they do not do work until an answer is requested, and they do not mutate the original data.

The amount of views available is quite good, and you can see a good list [here](https://ericniebler.github.io/range-v3/#range-views), so let me just point out some of them that I find particularly useful:

```C++
view::cartesian product
```

Description: `Enumerates the n-ary cartesian product of n ranges, i.e., generates all n-tuples (e1, e2, ... , en) where e1 is an element of the first range, e2 is an element of the second range, etc.`

See an example in action on [Wandbox](https://wandbox.org/permlink/e2DL67RkcDtlSKMw).

------

```C++
view::concat
```

Description: `Given *N* source ranges, produce a result range that is the concatenation of all of them.`

I especially like this one as it enables us to perform operation on a number of different ranges without having to actually concatenate them one to the other, see the [example](https://wandbox.org/permlink/T5JhvniI2kRdI4V7)!

------

```C++
view::filter
```

Description: `Given a source range and a unary predicate, filter the elements that satisfy the predicate.`

I already used that in my [first post](https://bznein.github.io/general/programming/c++/c++20/2019/08/01/Ranges-Library.html) about ranges, it's a basic and extremely view that will be used countless times.

------

```C++
view::zip
```

Description: `Given N ranges, return a new range where Mth element is the result of calling make_tuple on the Mth elements of all N ranges.`

```C++
view::zip_with
```

Description: `Given N ranges and a N-ary function, return a new range where Mth element is the result of calling the functions on the Mth elements of all N ranges.`

`zip` is a well know function for people coming from, for example, Python, and enables us to write something like that (in Python):

```python
Y1 = [1,2,3]
Y2 = [4,5,6,7]
for x1,x2 in zip(Y1,Y2):
    print(x1,x2)
```

which outputs `(1,4) (2,5) (3,6)`.

Until now, we missed a standard way to do that (it was possible in Boost). Now, we can easily 

do that:

```c++
  std::vector<int> v1{1,2,3};
  std::vector<int> v2{4,5,6,7};
   
    for (auto i:  ranges::view::zip(v1,v2))
        std::cout <<i.first << " " << i.second << std::endl;
```

which produces the same output!

And of course, we can combine them into a single value, if needed, with `zip_with`:

```c++
  std::vector<int> v1{1,2,3};
  std::vector<int> v2{4,5,6,7};
   
  auto multiply = [](int a, int b) {return a*b;};
    for (auto i:  ranges::view::zip_with(multiply,v1,v2))
        std::cout <<i << std::endl;
```



Which will output

```
4
10
18
```


#### Wrap it up
I hope that I showed you enough Range coolness to keep you interested!
There is so much more to it that I plan to dedicate more posts to this library, so stay focused! Next time, probably, I will talk about actions!

