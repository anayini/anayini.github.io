---
layout: post
title: Sorting by multiple properties in Swift
tags: ["ios", "swift"]
---

While working on some code at work, I ran into a simple sorting problem
in some of my Swift code.  Suppose we have a struct like this:

```swift
struct User {
    let uid: UUID
    let firstName: String
    let lastName: String
    let age: UInt8

    init(firstName: String, lastName: String, age: UInt8) {
        self.uid = UUID()
        self.firstName = firstName
        self.lastName = lastName
        self.age = age
    }
}
```

Let's say that we then have a `[User]`. How could we sort that array **firstly** by the `User`'s `lastName` **ascending** and, in the case of ties, **secondly** by the `User`'s' `age` **ascending**?

Well, if we can map our structure into a tuple of the properties we care about, then we can use the standard library's tuple comparison functions!  The standard library has several overloads of `<` for various sized tuples that are composed of `Comparable` types.  These implementations will compare the first values of the tuples together, and, in the case of a tie, continue to compare the next tuple values.

In order to sort by last name and break sorting ties with age, we map our
 users into tuples of `(lastName, age)` and then compare them with `<`:
```swift
let users = [
    User(firstName: "James", lastName: "Smith", age: 27),
    User(firstName: "Michael", lastName: "Smith", age: 30),
    User(firstName: "Maria", lastName: "Garcia", age: 28),
    User(firstName: "James", lastName: "Johnson", age: 26)
]

let sortedUsersByLastNameAscendingAgeAscending = users.sorted { u1, u2 in
    return (u1.lastName.lowercased(), u1.age) < (u2.lastName.lowercased(), u2.age)
}
```

A problem here is the potential for want to sort different dimensions of your tuple in different sort orders.  For example, supposed we wanted to
sort **firstly** by last name **ascending**, but **secondly** with age **descending**.  This is the type of problem that came up at work.  Luckily I was able to think of a small trick to make this possible.  When you are generating your tuples for sorting, simply switch the value you would normally place in the first tuple with the value you would normally place in the second tuple.

```swift


let sortedUsersByLastNameAscendingAgeDescending = users.sorted { u1, u2 in
    return (u1.lastName.lowercased(), u2.age) < (u2.lastName.lowercased(), u1.age)
}
```

Note the subtle change of order where `u2.age` is in the left tuple!

Of course small tricks like this can lead to very concise code, but are often either hard to read, or, even worse, they go unnoticed by the reader.  In the case of using a trick like this, I think it is worth
adding a comment to the code.
