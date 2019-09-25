---

layout: post
title: Push_back and its younger brother
categories: [general,programming,C++]
fullview: false
comments: true

excerpt_separator: <!--more-->
---

### Let's try one thing

Do me a favour. Open up a project, any project, and search for all the occurrences of (std::vector)`push_back`.

Done? Write down the number of results.

Now do the same with `emplace_back`. Compare the results.

<!--more-->


I'm ready to bet that the first search gave *way* more results than the second one. Why?

The reason might be historic (`push_back` has been around since forever, while `emplace_back` is a **C++11** addition), and there might even be a good reason for that (more of that later). But usually the real reason is simple and a bit saddening: *people have been thought to use `push_back`, or started using C++ before `emplace_back` existed, and they never learned why they should use `emplace_back`*.



### So, what's the difference?

This is the signature of `push_back`:

```c++
void push_back( const T& value ); (1)
void push_back( T&& value )       (2)
```

while `emplace_back` looks like this (until C++17):

```c++
template< class... Args >
void emplace_back( Args&&... args );
```

If you are familiar with *C++* (and [variadic templates](https://en.cppreference.com/w/cpp/language/parameter_pack)) you should have already spotted the difference. While `push_back` takes a value and appends it to the container (possibly copying it), `emplace_back` gets a parameter pack and forwards it to the constructor.



#### What does that mean in practice?

It means that `push_back` will potentially have to create an object and then **copy** it to the vector, while `emplace_back` will forward the arguments to the constructor and create the object directly inside.

Let's try it on a piece of code

```c++
#include <iostream>
#include <vector>

int emptyConstructor = 0;
int normalConstructor = 0;
int copyConstructor = 0;

class Person
{
private:
    std::string firstName_, lastName_;
public:
    Person() {
        ++emptyConstructor;
    }

    Person(std::string firstName, std::string lastName):
        firstName_(firstName),
        lastName_(lastName)
    {
        ++normalConstructor;
    }

    // Copy constructor
    Person(const Person& p2) {
        firstName_ = p2.firstName_;
        lastName_ = p2.lastName_;
        ++copyConstructor;
    }
};

int main()
{
    const int N = 40000;
    std::vector<Person> person1;
    person1.reserve(N);
    for (int i = 0; i < N; i++) {
        person1.push_back(Person("firstName", "lastName"));
    }
    
    std::cout << "--------- push_back results ----------\n";
    std::cout << "Empty constructor called " << emptyConstructor << " times.\n";
    std::cout << "Normal constructor called " << normalConstructor << " times.\n";
    std::cout << "Copy constructor called " << copyConstructor << " times.\n";

    emptyConstructor = 0;
    normalConstructor = 0;
    copyConstructor = 0;

    std::vector<Person> person2;
    person2.reserve(N);
    for (int i = 0; i < N; i++) {
        person2.emplace_back("firstName", "lastName");
    }
    
    std::cout << "\n\n--------- emplace_back results ----------\n";
    std::cout << "Empty constructor called " << emptyConstructor << " times.\n";
    std::cout << "Normal constructor called " << normalConstructor << " times.\n";
    std::cout << "Copy constructor called " << copyConstructor << " times.\n";

    return 0;
}
```



If you run this code, you will get the following result:

```
--------- push_back results ----------
Empty constructor called 0 times.
Normal constructor called 40000 times.
Copy constructor called 40000 times.


--------- emplace_back results ----------
Empty constructor called 0 times.
Normal constructor called 40000 times.
Copy constructor called 0 times.
```

(see the code in action and play with it [here](https://wandbox.org/permlink/cRRxp388US57WIGB))



Since `push_back`is receiving a temporary object, it needs to copy it into the vector, so each call to `push_back`actually implies two object constructions: the first one is at `Person("firstName", "lastName"))`, and the second one is the one of the copy constructor that is called inside `push_back`to copy the object inside the vector. 

`emplace_back`, on the other side, is called by directly passing it the arguments that get forwarded to the constructor of `Person`, thus avoiding the need for the copy constructor to be called!


#### C++17 addendum

There is another nice, little reason to prefer `emplace_back` over `push_back`, and it is a change to its signature since *C++17*.

From 

```c++
template< class... Args >
void emplace_back( Args&&... args );
```

to

```c++
template< class... Args >
reference emplace_back( Args&&... args );
```

The function now returns a reference to the inserted object. That allows us some nice chaining, as we can now access an object we just inserted, from a temporary, inside our vector, without having to pass through `std::vector::back()`.

As an example, let's add to our `Person`class a method to change its first name:

```c++
  void changeFirstName(std::string newName){
        firstName_ = newName;
    }
```

And suppose that, for whatever reason, we want to change the first name of each object we insert, right after inserting. If we are using `push_back`there is no better way do that than:

```c++
for (int i = 0; i < N; i++) {
    person1.push_back(Person("firstName", "lastName"));
    person1.back().changeFirstName("newFirstName");
}
```

while `emplace_back`gives us a better, cleaner alternative:

```c++
for (int i = 0; i < N; i++) {
    person2.emplace_back("firstName", "lastName").changeFirstName("newName");
}
```

(code [here](https://wandbox.org/permlink/zDjme80KuNXrmOe0))



### So, should I completely ditch push_back in favour of emplace_back?

![Well..](https://i.kym-cdn.com/entries/icons/original/000/028/596/dsmGaKWMeHXe9QuJtq_ys30PNfTGnMsRuHuo_MUzGCg.jpg)

If you do that, I would say you will be completely fine 99% of the cases... however there are some cases in which `emplace_back`might not be the correct choice, and that would mostly come down to *implicit* vs *explicit* constructors... something we might want to talk in a future post.



