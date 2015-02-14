---
layout: post
categories: [fp,unison]
title: Unison update 2&colon; connecting the editor to the node
---

As I mentioned in [week 0](/2015-01-30/unison-update0.html), the Unison node is written in Haskell and has an implementation of the Unison language, its typechecker, and any primitive functions. It exposes an API over HTTP. [Last week](/2015-02-06/unison-update1.html), I worked on some of the Unison editor interactions, but I was working somewhat in a vaccum since the editor wasn't yet connected to the Unison node. I spent this week actually getting the editor and the node talking to each other, and refining the node API a bit in the process.

Sadly, I don't have a demo to show. At one point, I had a round trip sucessfully working, with the editor getting live info from the typechecker, but after that I decided to rework some of the node API to better facilitate the interactions I wanted. I've just completed that work today but haven't hooked the changes back up to the editor. I hope to have something more compelling to show next week.

One of the questions I was thinking through on the node API is how to handle search, and at what scope to do this search. In Unison, terms are uniquely identified by a nameless hash (more on that in a later post) and don't live in packages or modules. Instead, we may search for terms in various ways, including by "name". A term may have multiple names, and different nodes may refer to the same hash with different names as names are just metadata stored separately. (A "package" could be a concept, but again, it's just a metadata thing which points to a collection of hashes) This raises interesting questions, though: if the user has clicked on a node to edit it, what completions should they be shown? Here are some possible answers:

* All possible terms that typecheck and match the search query (could be a huge set)
* Only 'local' terms that typecheck and match the search query (requires an explicit imports action)
* Something in between

In raw text editing, it's necessary to disambiguate references with explicit imports. Some IDEs will automate this for you, but the model is the same; the presentation of the program must disambiguate user intent, leading to 50 lines of imports at the top of every "file", where its often arbitrary how codebase is split up into files. In a semantic editor, we can dispense with the ceremony of imports and instead let the user disambiguate at the point of reference. Here's a very simple proposed interaction:

* Upon opening a node to edit, we search local scope based on the search query.
* If the search query starts with `"?"` we include global scope as well. If the number of matches is too high, we display only the count.

Notice that unlike using something like Hoogle "manually", we don't have to enter the type, since the editor knows the type of the current edit location and can supply that information for us! Thus, we just focus on specifying whatever metadata we'd like to search by.

There are some further refinements to this basic interaction to improve the ability to quickly refine our search. I'll discuss these in a later post.

Looking forward to next week!
