---
layout: post
categories: [machine learning]
title: Learning how to make a thing learnable is harder than learning it
---

Here's something pretty crazy: there are around 10^28 atoms in the human body. An astromically tiny percentage of arrangements of 10^28 atoms in 3D space correspond to a viable human. Most arrangements of atoms would just melt into a puddle!

If you were trying to learn something as complex as a human body, with that many parameters, you'd need to transform the problem to a much simpler one, where the space of possibilities is much much smaller and where more of these possibilities have nonzero fitness. This allows the problem to be learned.

Remarkably, evolution has managed to do this: there is an incredibly complicated mapping from the human genotypes to the human body. Even more crazy, that mapping was itself learned by evolution! This has to be the most impressive thing about evolution - this seemingly dumb process converts what would otherwise be an insanely complex and huge problem space into a much simpler one where that same dumb process can be effective at learning.

Learning is really two things: step 1 is learning how to make the problem learnable, and step 2 is doing the learning. In machine learning, it's usually the humans that figure out how to make the problem learnable, by applying our smarts to the problem to arrive at some highly constrained representation which is learnable via dumb algorithms. But step 1 is the hard part!

Maybe the reason we often overlook step 1 is that our brains are so good at reducing very complex problems to simpler spaces. We do our thinking in that simpler space and then project results back to the more complex space. For instance, in order to write this post, I definitely don't think in the space of all possible sequences of characters. Instead I have thoughts which I translate via some complex mapping to actual characters. When I walk, I don't think about controlling all the gajillions of nerve outputs directly, I execute some very high level movement plan which is translated to nerve outputs via some very complicated mapping. And so on...

In each of these cases, the hard part is actually coming up with this mapping or learning it in the first place!
