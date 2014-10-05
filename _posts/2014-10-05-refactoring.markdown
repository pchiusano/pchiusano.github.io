---
layout: post
categories: [fp, tech, unison]
title: Making overly conservative language evolution a nonproblem
---

There was a [recent thread on the Haskell reddit](http://www.reddit.com/r/haskell/comments/2icjmf/how_to_rewrite_the_prelude/), sparked by a [Neil Mitchell post](http://www.reddit.com/r/haskell/comments/2hzqii/neil_mitchells_haskell_blog_why/) arguing that various functions in the Haskell Prelude should not be generalized.

I have my own personal opinions on the changes being considered, and I'm sure many people do. But here, I want to make an observation--there is no _fundamental_ reason why a language should force everyone to agree on the correct signature for a function like `mapM`, or any other function for that matter. That problem is incidental complexity, created artificially because:

1. All types and values are dumped into a global, hierarchical namespace shared by all libraries, thereby forcing people to agree on what names should be mapped to what values.
2. In the event that any changes are made, there is no good story for how the community of users can easily upgrade their code short of manual text munging. As a result of how painful this is, the leads the community to be overly conservative in what changes are even considered.

That is, these problems can be solved with better technology, by revisiting some of the foundational assumptions that go into the underlying programming platform. In Unison, terms and types are identified uniquely by a nameless hash, with names stored as separate metadata. We need not all agree on the metadata associated with each term (though of course we may find that is useful for communication purposes). This sidesteps a lot of problems, and also sets the stage for developing a much richer refactoring story for making large codebase transformations in an automated way.

I very much prefer solutions like this--rather than force everyone to agree and reach some compromise that won't satisfy everyone, find better technology that lets evolution occur more independently. The community isn't held back by overly an overly conservative approach to making changes, and individual users aren't necessarily forcibly dragged along or forced to comply with changes they want no part in.
