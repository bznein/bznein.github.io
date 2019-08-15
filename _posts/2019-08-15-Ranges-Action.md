---

layout: post
title: Ranges, action!
categories: [general,programming,C++, C++20]
fullview: false
comments: true

excerpt_separator: <!--more-->
---



##  How Ranges actions can make code more readable

In the last [few](https://bznein.github.io/general/programming/c++/c++20/2019/08/01/Ranges-Library.html) [posts](https://bznein.github.io/general/programming/c++/c++20/2019/08/08/Ranges-Coolness.html) I focused on Ranges views and how they make code more clear and readable.

Well, as it turns out, this is true also for actions, maybe even more!
<!--more-->



### What are actions?

[Actions](https://ericniebler.github.io/range-v3/index.html#range-actions) are eager range combinator (in contrasts with views, which are lazy) that allow to modify the source range. There's a number of available actions, mostly with a similar intended use to some STL algorithm, but with the great advantage of composability.

Let's see that in action; suppose we have a vector of `int` and we want to print only its unique entries, in increasing order.

Before ranges, that would have looked like this

```c++
std::vector<int> v{ 11, 21, 5, 9, -2, 0, 7 };
    
std::sort(std::begin(v), std::end(v));
v.erase( std::unique(std::begin(v), std::end(v)), std::end(v));
for (const int elem: v)
     std::cout << elem << std::endl;
```

With the usual abundance of explicit iterators.

While the same thing with ranges can be achieved as follows:

```c++
namespace rs = ranges::v3;
namespace rv = ranges::v3::view;
namespace ra = ranges::v3::action;

std::vector<int> vv{ 11, 21, 5, 9, -2, 0, 7 };
for (const int elem: std::move(vv) | ra::sort | ra::unique )
      std::cout << elem << std::endl;
```

A one-liner that makes things easy, clear, and concise! 

See in in action [here](https://wandbox.org/permlink/kaT1jD2uSfA1yvpw).



There are many more actions which would basically cover most of your use cases and make your code much clearer, so let me focus on some ones.



#### Remove the two smallest and two largest values of a vector of integers

Let us assume here that more than 4 elements are present in the vector. 

In plain old style, things get really ugly real fast:

```c++
std::vector<int> v{ 11, 21, 5, 9, -2, 0, 7 };
    
std::sort(std::begin(v), std::end(v));
auto first = std::begin(v);
std::advance(first,2);
auto last = first;
std::advance(last,std::size(v)-4);
v.erase(last, std::end(v));
v.erase(std::begin(v), first);
for (const int elem: v)
    std::cout << elem << std::endl;
```

This is hard to read and it is not absolutely clear at first sight what this code is intended to do; we have two calls to `advance` to get the iterators in place and two two `erase` to get rid of the unwanted elements.

Let's compare it to the  **Ranges** counterpart (please note, the namespaces are not defined anymore and are the same of the previous example):

```c++
std::vector<int> vv{ 11, 21, 5, 9, -2, 0, 7 };
for (const int elem: std::move(vv) | ra::sort | ra::slice(2,rs::end-2))
    std::cout << elem << std::endl;
```

Another simple, clear, one-liner that leaves no doubts on the intended use. The pipe syntax clearly shows what is to be expected: the vector `vv` gets [`moved`](https://en.cppreference.com/w/cpp/utility/move) (as this is needed by the range adaptors), then sorted, then sliced from `2` to the end of range minus `2`.

Simple, clear, self-explanatory. See it in action [here](https://wandbox.org/permlink/xbHITzAB8Qw6Kqhr)!



#### Count the number of space-separated words in a string

I love this one as string processing in C++ has never been particularly easy:

```c++
auto text = "A list of words separated by a space and we want to count them";

int count = 0;
std::string token;
std::istringstream stream(text);
while (std::getline(stream,token, ' '))
    ++count;
```

This might even not be particularly ugly, but **why** do we have to define a token, a stringstream **and** use a manually written for loop for such a trivial task?

In fact, we don't! Not with Ranges:

```c++
std::cout << rs::distance(rv::c_str(text) | rv::split(' '));
```

This solution is so insanely much better that it blows my mind! One line, three calls to range functions, and that's it!



But what is exactly going on here? I will admit, this time, that things aren't as clear as they usually are. Let's break it into simpler pieces:

`rv::c_str(text)` is a [very simple view](https://ericniebler.github.io/range-v3/structranges_1_1view_1_1c__str__fn.html) to see a `\0`-terminated string as a range. 

`rv::split(delimiter)` does pretty much what you would expect: given a source range and a delimiter, splits the source range into a range of ranges using the specified delimiter.

What about `rs::distance`? Admittedly, not much documentation is available for ranges at the moment, so if you are trying to found out what this does, you might find yourself digging into pages and pages of google searches before finding the answer. 

The answer I came up with, after seeing it used for the first time [here](https://stackoverflow.com/questions/44413446/why-is-range-v3-slower-than-the-stl-in-this-example), is that it is calling `rs::distance` on a range `r`, is the equivalent of calling, on a container `c`, the following: `std::distance(c.begin(), c.end())`. Which means that, given that `rv::split` returns a range of ranges, we get as a result the number of ranges generated by split, i.e. the number of words in our text.

If this is incorrect, please tell me and guide me towards a better description of `distance`, please!



As usual, see the example [here](https://wandbox.org/permlink/QoabIwsPw0nuGQuU)!



##### Wrapping it up

This might be, for a while, my last post about **Ranges**, I hope I made it clear how much I love this library and how much I think the code everyone writes will benefit from it. I really hope to see an early adoption of C++20 in every codebase!

Next time, I will probably discuss about [{fmt}](https://github.com/fmtlib/fmt). Why? Because that provides now the implementation for another long-awaited feature of C++20, `std::format`!



