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

Let's say that we have a `[User]`. How could we sort that array **first** by `lastName` **ascending** and, in the case of ties, **second** by the `age` **ascending**?

Well, if we can map our structure into a tuple of the properties we care about, then we can use the standard library's tuple comparison functions!  The standard library has [several overloads](https://github.com/apple/swift/blob/master/stdlib/public/core/Tuple.swift.gyb#L188) of `<` for various sized tuples that are composed of `Comparable` types.  These implementations will compare the first values of the tuples together, and, in the case of a tie, continue to compare the next tuple values.

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

You may, however, want to sort different dimensions of your tuple in different sort orders.  Suppose we wanted to
sort **first** by `lastName` **ascending**, but, in the case of ties, **second** by `age` **descending**.  This is the type of problem that came up at work.  Luckily, I was able to think of a small trick to make this possible with minimal code.  Simply switch the value you would normally place in the first tuple with the value you would normally place in the second tuple.

```swift
let sortedUsersByLastNameAscendingAgeDescending = users.sorted { u1, u2 in
      // We are sorting by `lastName` ascending, but we are
      // breaking ties based on `age` in descending order.
      // Notice that u2.age is in the left tuple!
    return (u1.lastName.lowercased(), u2.age) < (u2.lastName.lowercased(), u1.age)
}
```

Tricks like this can lead to very concise code, but are often either hard to read or can go unnoticed by the reader.  In the case of using the above sorting technique, I think it is worth adding a comment to call attention to what you are doing.
