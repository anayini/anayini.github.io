---
layout: post
title: iOS Network Debugging With Charles Proxy
tags: ["ios", "swift", "debugging"]

---

I was recently working on a project where I encountered a bit of slow down in
debugging some API calls.  The project can be simplified as grabbing data from a
new API, parsing and persisting the data, and rendering it in a `UITableView`.
In this particular case, the development of the backend API was being done by a separate
set of people and was using a new backend infrastructure separate from the old API
infrastructure.  This meant that the requests going out didn't have the standard
structure that I was used to.

A few problems arose in nailing down the shape of the HTTP request, which I solved
simply by using `lldb` to break in our networking code.  Still, it involved breaking in some
delegate methods and translating my result to something I could easily communicate to the backend
team was a bit of an annoyance.  In this case it would have been a lot simpler to just intercept and
inspect the requests with a tool.  Enter **Charles Proxy**.

### Charles Proxy ###

Charles Proxy is a web debugging proxy.  It is a tool you can run on your machine that can
capture out going network requests and incoming responses and allow you to inspect and even modify them.
You can download Charles Proxy here.

To get setup debugging Charles has to intercept your requests.  It can basically do this out of the box.  All
you need to do is run the program, open your simulator and make a network request.  In most cases though,
you are (hopefully) using HTTPS, and in order to intecept these requests, we need to ensure that the proper
certificates are installed on you simulator.  To do that there are a few simple steps:

1.  From the `Proxy` menu, choose `SSL Proxying Settings`, select the checkbox to `Enable SSL Proxying`,
and add an entry for the host and port you want SSL proxying for.  Wildcards are supported.
2. From the `Help` menu of Charles, select `Help > SSL Proxying > Install Charles Root Certificate on iOS Simulators`.
This will automatically install the Charles Root Certificate into your simulators which will allow the SSL trust
chain to work.
3.  You may also need to go to your Simulator and into `Settings > General > About > Certificate Trust Settings` and
`Enable Full Trust For Root Certificates` after installing the Charles Root Certificate.  This may be done by default
on some versions of iOS.

From here you should be able to make an HTTPS request and see it appear in the Charles UI.  I recommend switching
to the `Sequence` view instead of using the `Structure` view as I find it easier to read.

And thats about it!  It is unlikely that you'll need to use this daily, but there are times it comes in very handy and its
a great tool to have in your toolbox.
