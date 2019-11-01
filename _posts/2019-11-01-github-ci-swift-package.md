---
layout: post
title: Swift Package Continuous Integration Tests with Github Actions
tags: ["ios", "swift", "continuous integration", "spm"]
---

Inspired by [a project we open sourced at PlanGrid](https://github.com/plangrid/ReactiveLists), I've been working to create [ANReactiveTableKit](https://github.com/anayini/ANReactiveTableKit), a small Swift library that provides a React style API for `UITableView` in iOS 13.

I'll be writing a separate post on that in the future, but for now I wanted to
write a note about running tests from Xcode using [Github Actions](https://github.com/features/actions).

While many Swift packages contain pure logic, [ANReactiveTableKit](https://github.com/anayini/ANReactiveTableKit) is closely tied to `UIKit` and thus requires the iOS system libraries to build and test.  Thus, the easiest way to run our tests is from Xcode.

To this we will need to:

1. Ask Github Actions for a macOS machine.
1. Ensure we are using the version of Xcode we need (Xcode 11 in this case).
1. Generate an .xcodeproj file.
1. Use xcodebuild (which requires the xcodeproj) to run the tests on a simulator.

This is all actually possible today with Github Actions.

Here is the initial workflow that I've been using:

```yml
name: Unit Test

# We will trigger this workflow whenever someone pushes to the repo.
on: [push]

jobs:
  build:

    # We want our unit tests to be run on the latest available version of
    # macOS that Github Actions supports.
    runs-on: macOS-latest

    steps:
    - uses: actions/checkout@v1
    # Github Actions' machines do in fact have recent versions of Xcode,
    # but you may have to explicitly switch to them.  We explicitly want
    # to use Xcode 11, so we use xcode-select to switch to it.
    - name: Switch to Xcode 11
      run: sudo xcode-select --switch /Applications/Xcode_11.1.app
    # Since we want to be running our tests from Xcode, we need to
    # generate an .xcodeproj file.  Luckly, Swift Package Manager has
    # build in functionality to do so.
    - name: Generate xcodeproj
      run: swift package generate-xcodeproj
    # Finally, we invoke xcodebuild to run the tests on an iPhone 11
    # simulator.
    - name: Run tests
      run: xcodebuild test -destination 'name=iPhone 11' -scheme 'ANReactiveTableKit-Package'
```
