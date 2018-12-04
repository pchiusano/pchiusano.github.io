---
layout: post
categories: [fp, programming, tech]
title: What is really the root cause of the event-stream vulnerability?
---

I was trying to make sense of the [event-stream vulnerability](https://schneid.io/blog/event-stream-vulnerability-explained/). My take:

* Every JS script gets access to the DOM, a global object from which you can scrape every piece of information on the page. You can explore the DOM just by making up _strings_ at runtime and rooting around in the DOM, so it can't even be statically known what information JS code accesses. Yikes!
* Scripts are just loaded via a JS function call (like `require`) to which you pass a _string_. Also a dangerous capability since it means the dependencies of a piece of JS code can't be statically known.

So you have this global, dynamically accessible object containing all sorts of sensitive information, which every little script on the page gets access to, including the library you expect to be doing only pure computation on the data you explicitly pass it. It's extremely difficult to secure this: JS access to the DOM is analogous to C's feature of being able to poke at arbitrary portions of memory, which is difficult to secure for similar reasons.

It's probably too late for JS, but if you were redesigning a language for the web and had [more than 10 days to do it](http://speakingjs.com/es5/ch04.html), it would be possible to completely eliminate these possibilities in the first place. Instead we're stuck with the consequences of a system that's almost impossible to secure.

People have been quick to blame the maintainer who handed maintenance over to someone who proved to be a bad actor, or other people or process issues. No, those aren't the root problems. We should expect that people will make mistakes and instead question the system and core tech which allows these mistakes to be amplified and propagated at such enormous scale.
