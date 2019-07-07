---
title: Learning without a gradient (part 1)
layout: post
categories: [machine learning]
---

Learning using gradient-based methods is a lot like trying to find the top of Mount Everest starting from a random point on earth, __while blindfolded__, by repeatedly sampling only the altitude and slope of the ground you're standing on, and then using that information to decide where to teleport next.

Imagine you've got your blindfold on, and you note that the ground slopes upward if you travel northeast. How far should you go in that direction? The blindfold prevents you from just looking off in the distance and observing "oh, it keeps going upward in that direction for a while". You can only sample the height and slope at the individual points you teleport to.

So maybe you walk 10 feet, and the ground continues sloping upward in that direction. Should you teleport half a mile along that same vector and take another sample? 10 miles? 100 miles? What if you teleport too far and skip right past the highest point? Or maybe you only move 1 foot at a time, but it takes you eons to get anywhere. Should you continue along a vector even if you reach a point where it starts leading downard? Or do you keep some "momentum" and hope that was just a temporary little dip in the landscape?

There aren't really good answers to these questions and a lot of it comes down to: "I tried XYZ on these problems, and it seemed to work okay" and some [rules of thumb](https://machinelearningmastery.com/learning-rate-for-deep-learning-neural-networks/) for how to discover "good" hyperparameters for your problem. 

__The gradient just isn't a lot to go on.__ The local slope at a point in Kansas might help you reach the top of the hill near where you started, but it doesn't help in locating even the general region where Mount Everest is located.

Most optimization problems are actually harder than the above analogy lets on. The function being optimized will have more than just two parameters, and the landscape may be very “rugged.” It's often expensive to compute your current altitude and the current slope, so we settle for an estimate of these things. Extending our analogy, imagine if you didn't even have an accurate way of measuring current altitude or slope, but instead had lots and lots of unreliable measuring devices whose results you could aggregate (with much effort) to produce more accurate readings.

For instance, in deep learning, when training a network with a gajillion tunable parameters on a training set that's 100k elements, calculating the actual "altitude" of your current location would involve evaluating the current network output for all 100k elements of the training set. Similarly for estimating the gradient: we can determine what direction to adjust weights to improve average performance for a batch of examples, but we don't know for certain that [improvement on this batch](https://www.quora.com/Intuitively-how-does-mini-batch-size-affect-the-performance-of-stochastic-gradient-descent/answer/Brian-Wong-2) won't just make performance on the overall training set worse!

### You gotta make assumptions, and what, precisely, are they?

A takeaway from the above discussion is that any learning algorithm has to make some assumptions about the space it is optimizing, if it is to be effective. For instance, in our blindfolded Everest-finding example, knowing something about how the structure of the earth's surface is helpful.

For instance, the earth's surface altitude may change rapidly at a local scale, but can be quite smooth if we apply a low pass filter and average the altitude of neighboring regions. Mount Everest isn't just a single spike in the middle of a flatlands, it's a huge mass of land which must be supported physically, so it (and any other tall mountain peak) tends to be surrounded by regions of similar altitude.

There's a famous set of theorems, the ["No Free Lunch" (NFL) theorems](https://en.wikipedia.org/wiki/No_free_lunch_theorem), which amount to a formal statement about the need for optimization algorithms to make assumptions about problem structure. The first of the NFL theorems states that "any two optimization algorithms are equivalent when their performance is averaged across all possible problems". So if you consider "all possible problems", all optimization algorithms, including random search, give equivalent performance! My intuition for the NFL results is that for every problem where your fancy optimization algorithm does well, there's another problem (in that huge space of all possible problems) where the assumptions your algorithm makes are actively harmful!

The NFL theorems were a pretty explosive result, because up until then people were making all sorts of unqualified statements about their learning algorithm being "better" after getting bettter results on a few benchmarks. NFL makes it clear: no optimization algorithm is _better_, only better for _some kinds of problems_.

A common response to NFL is that: "well, we aren't solving problems randomly chosen from the space of all possible ones; we're solving real world problems which have quite a bit more structure".

But the way I like to understand the NFL results is that _every optimization algorithm must exploit some assumed "structure" to the functions being optimized_. If you can more precisely characterize the structure of the function being optimized, then you can pick the right algorithm for the job, the one that exquisitely exploits this structure to find very good solutions quickly.

### Next time

For the next post, I'm going to use our same "find Mount Everest while blindfolded" metaphor to explore a class of learning algorithms that _doesn't use gradient information at all_.
