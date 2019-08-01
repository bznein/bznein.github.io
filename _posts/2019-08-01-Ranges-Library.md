---
layout: post
title: Ranges.. now what?
categories: [general,programming,C++, C++20]
fullview: false
comments: true


---

If you are just a bit like me, you are eagerly waiting for **C++20** to be released and you are constantly browsing websites and blogs to discover new features.

And, oh boy, there will be **a lot** of new features in the next release of our favourite programming language! 

For a nice summary, go to the worldwide famous **[Sutter's Mill](https://herbsutter.com/2019/07/20/trip-report-summer-iso-c-standards-meeting-cologne/)**'s blog and look at that amazing list of new, cool features! 

There is so much stuff going on that is hard to wrap your head around it: `std::format`, `spaceship operator`,  `modules`, `coroutines`, `ranges`... Covering all of them would be an immense task, and furtherly I don't think I would do better than the much more experienced people who already discussed them in details all around the internet.

Still, I would like to discuss some of them in my own way: I always like to look at new features and think how they could be inserted in some older code. So, instead of *teaching* anyone how some of these features work, I will show you a lot of **examples**, hoping that you would be able to benefit from them as much as I do while writing them!

For the next few weeks, I will sometimes interrupt my regular posts about last year's [Advent Of Code](https://bznein.github.io/general/programming/challenges/2019/07/25/I-Survived-The-Advent-Of-Code.html) by writing about some of these features, starting today!



# Ranges 

So, as the title says, today I'm going to talk about the feature that, by far, excites me more among the upcoming **C++20** addition, [Ranges](https://en.cppreference.com/w/cpp/ranges)!



### Ok, but what the heck is a Range?

Good question! 

Let's try to think of it as a powered-up iterator. You can think of an iterator *almost* as if it was a pointer (just almost, but actually an iterator [is not a pointer](https://stackoverflow.com/questions/32654108/c-stdvectoriterator-is-not-a-pointer-why)); whit this similitude, a **range** can be seen as a pair of iterators `[begin,end)`.

Yes, those two iterators so annoying to write every time you call some **STL** algorithm!

With Ranges, you can (almost) always forget about them!

Let's start with a simple example: suppose you have a class *Person* representing, you guessed it, a person. A super-simplified version of it:

```c++
class Person
{
public:
    std::string firstName;
    std::string lastName;
    int age;
}
```

and somewhere you have a `vector<Person> persons`, from which you want to extract every person whose age is `>=18`. In the good old fashioned STL-way you would write something like that:

```c++
std::vector<Person> g18; 
auto isGreaterThan18 = [](const Person& p){return p.age>=18;};

std::copy_if(persons.begin(), persons.end(), std::back_inserter(g18), isGreaterThan18});
```

This is not bad, granted, but let's see how that would look like with **Ranges**:

```c++
auto isGreaterThan18 = [](const Person & p){return p.age>=18;};
auto g18 = persons | ranges::v3::view::filter(isGreaterThan18);
```

This looks so much better, clear and clean!

But exactly, what is going on here?

First of all, let's rewrite that second line in an easier to understand way:

```c++
auto g18 =ranges::v3::view::filter(persons,isGreaterThan18);
```

This line is analogous to the previous one, but with a syntax that C++ programmers will find much more familiar; here we can see that `filter` takes a *container*, in our case a `vector<Person>`, and a boolean function, in our case `f : Person → bool` and returns[^1]  a `vector<Person>` containing only the elements `e`of the original vector for which  `f(e) = true`.

[^1]: it doesn't actually return the vector. Instead, the correct term is `View`, but I won't go into details now, maybe in another post.



#### This looks cool right?

Well, wait for chaining of range adaptors!

Let's imagine that you want to perform some action on the objects you just extracted. Maybe you want to set their last name to an empty string for privacy reasons.

In the old style, you would  perform a classic loop:

```c++
for (auto & p : g18)
        p.lastName="";
```

But how that would look like in **Ranges**? Well, I can't just write here the additional lines, because range adaptors can be combined to perform sequences of filtering and transformations in a single line of code!

Take a look here:

```c++
auto isGreaterThan18 = [](const Person & p){return p.age>=18;};
auto deleteLastName = [](Person & p) {p.lastName=""; return p;};

auto g18 = persons | ranges::v3::view::filter(isGreaterThan18) | ranges::v3::view::transform(deleteLastName);
```

This is **all** that is needed to perform the combination of operations we discussed here! 

##### (Short) summary

I hope that at least a very small part of the beauty of the **Ranges** library will be clear after reading this post. This is just an extremely small examples and we didn't go into any level of details, so it might just be a bit of an aesthetic improvement not worth all the excitement. However, I can guarantee you that is not like that!

I will make more post, in the near future, about the **Ranges** library , explaining more of their awesomeness! 
