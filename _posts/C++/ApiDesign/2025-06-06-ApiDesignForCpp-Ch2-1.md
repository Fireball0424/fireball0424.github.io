---
title: API Design for C++ Ch2 Part1
date: 2025-06-06 17:00 +0800 
categories: [C++, API Design]
tags: [Note, Design Pattern, C++]
---

## Provide a Good Abstraction

An API should provide a **logical abstraction** for the problem that is solves

1. Formulated in high-level concepts
2. Each class should have a central purpose → Refelcted in the name of the class & its methods

Review by others

## Hide Implementation Details

Any internal details that are most likely to change, must be kept secret from the client of the API

(change without affecting any existing clients)

### Physical Hiding

> Store internal details in .cpp
> Store public interface in .h
{: .prompt-tip}

### Logical Hiding

* Public: Accessible by everyone
* Protected: Accessible within the class and its derived classes only
* Private: Accessible within the class (Default)

> Encapsulation (封裝) is the process of separating the public interface of an API from its underlying implementation
{: .prompt-tip}

→ Use protected and private to restrict access to internal details

### Hide Member Variables

Instead of expose variables, use getter/setter methods to access member variables indirectly.

```cpp=
class Point{
public: 
    int GetX();
    int GetY();
    void SetX(int xval);
    void SetY(int yval);
    
    int CalSum(); // return x + y
private: 
    int x, y;
}
```

Benefits of getter/setter

1. Validation (Make sure set value makes sense)
2. Lazy evaluation (Calculating the value of variables whenever variable changes iscostly) → Ex: CalSum()
3. Caching
4. Extra Computation → Ex: Store user info into disk whenever they access data
5. Notification → Ex: When data change, also update GUI 
6. Debugging (Write logs)
7. Synchronization (mutual lock)
8. Finer Access Control (Provide a finer level of read/write control) → Ex: read-only not providing a setter method
9. Maintaining Invariant Relationships (Some variables have dependencies)

> Not even declare variables protected, otherwise, derived client class can still be affected whenever changing
{: .prompt-tip}

### Hiding Implementation Methods

Not only member variables, but also hide all methods that do not need to be public.

> A class should define what to do, not how it is done.
{: .prompt-tip}

<!------>

> Never return non-const pointers or references to private data members, which breaks encapsulation
{: .prompt-tip}

However, it still shows everything in header file.
> Prefer declaring private functionality as static functions within the .cpp file rather than exposing them in public headers as private methods
>
> Use the Pimpl idiom is even better enough
{: .prompt-tip}

### Hide Implementation Classes

Some classes that are purely implementation details should not be exposed to public Api

```cpp
class Fireworks{
public: 
    Fireworks(); 
    // Some functions 

private: 
    class FireParticle{
        public: 
            double mX, mY; 
            // Others variables
    }
    double mOriginX, mOriginY; 
    // Others variables of Fireworks
    std::vector<FireParticle *> mParticles; 
}
```

Note that, FireParticle doesn't use getter/setter methods, because the classes is not accessible for the public interface.

Moreover, it's possible to hide the contents of FireParticle class from appearing in the header file. (下一章會提到，可以再去看看)

Some people prefer to use struct to reflect that the structure is a Plain Old Data (POD) type.

#### POD

(Ref: Wiki)
簡單來說，指的是class, struct, union，並且不具有使用者定義的constructor, destructor, copy, assignment。No derived relationship (so no base classes), no virtual functions, non-static members are all public, no reference type, no non-POD type, no pointer-to-member-data.

C++ 有推廣定義成兩種（trivial & standard-layout），這之後再來補上這塊的筆記
POD的記憶體是連續的，還有其他很多特性

## 後記

* 這段中間有教 Unified Models Languages, 感覺有空也能回頭讀一下這段 + 其他細節，把這個學起來
* Pimpl idiom 也可以多了解一下
* 後面還有一大部分，下次繼續~~
