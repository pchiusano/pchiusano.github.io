---
title: Learning without a gradient (part 2)
layout: post
categories: [machine-learning]
---

[Part 1](/2019-07-06-learning-without-a-gradient) of this series discussed the difficulties faced by gradient-based approaches to learning: 

> Learning using gradient-based methods is a lot like trying to find the top of Mount Everest starting from a random point on earth, while blindfolded, by repeatedly sampling only the altitude and slope of the ground you’re standing on, and then using that information to decide where to teleport next... the gradient just isn’t a lot to go on. The local slope at a point in Kansas might help you reach the top of the hill near where you started, but it doesn’t help in locating even the general region where Mount Everest is located.

This fundamental issue of misleading local gradient information arises in higher-dimensional problems too, even though [local minima are a lot less common here](https://twitter.com/pchiusano/status/1181411170672136192).

Let's now look at a class of learning algorithms that don't need gradient information. Gradient-free learning methods are a big deal: they are more widely applicable (many functions of interest are not differentiable, including hyperparameter search for other learning frameworks), and even in cases where differentiability is possible in principle, learning without a gradient means no fancy frameworks just to build up functions in differentiable form. Instead, you can write the functions being optimized using ordinary code in your favorite programming language, then optimize those functions as a black box.

It might seem like we don't have much to go on if we aren't using gradient information... are we reduced to just random search? Or inefficiently estimating the gradient by sampling the output of "nearby" points, as in [Evolution Strategies](https://openai.com/blog/evolution-strategies/)? No, we actually have a lot to go on. Again to build intuition we'll stick with our original problem of finding the top of Mt. Everest, given only the ability to sample the altitude at any point on the globe.

## Algorithm 1

Rather than relying on local gradient information, this is the first algorithms that uses only _statistical properties of regions of the search space_:

1. Divide the current search space in half (say, northern and southern hemisphere). Sample random points from both halves, building up a sampled distribution of altitudes for each.
2. When a statistical test (like perhaps [this one](https://en.wikipedia.org/wiki/Kolmogorov–Smirnov_test)) shows with 99% confidence that (say) the northern hemisphere has higher average altitude, go to step __1__ but with the northern hemisphere as the current search space.
3. Repeat, dividing the current search space in half each time, until converging on an arbitrarily small region of space.

That's it! The current search space gets divided in half each time, and the search proceeds in the half with higher estimated average altitude. It's analogous to binary search, but using sampling to estimate which half contains the value we're looking for. Notice this algorithm is not really sensitive to local noisy features of the search space (it won't stop at the top of a hill in Kansas, for instance), until it reaches the end of the search and is confined to a much smaller region. This property is something that gradient-based search will sometimes fake by altering the step size over time.

One can imagine this algorithm converging to a point which isn't the global optimum, perhaps finding the top of [K2 rather than Mt Everest](https://en.wikipedia.org/wiki/List_of_highest_mountains_on_Earth), but we aren't going to worry about that: no algorithm can find a needle in a haystack, so being able to converge to any reasonable solution is a win.

The efficiency of this algorithm depends at each stage on how similar the distributions are of the two halves being compared. The more similar the distributions are in reality, the more samples we need to gather in order for our statistical test to determine with confidence which is better.

Armed with this information, let's build a better algorithm.

## Algorithm 2

A problem with Algorithm 1 is it makes an arbitrary choice of how to divide the current search space into two subspaces, and this choice significantly affects performance. Any time important decisions like this are made arbitrarily, that's a red flag for a learning algorithm. Instead, let's explore multiple choices in parallel:

1. For the current region, pick N random hyperplanes dividing the region roughly in half.
2. Begin sampling points in the current region. If any of the N subdivisions passes the statistical test where one half is better than the other, make that subregion the current region and go to step __1__.
3. Repeat as before.

This is better. Rather than randomly picking one (possibly terrible) way of cutting the space in half, we try multiple ways at the same time and can advance the search as soon as any gives an answer.

Still, we are picking these N hyperplanes randomly. And how did we come up with the number N anyway? Can we do better?

## Algorithm 3

Rather than picking N random ways of cutting the space in half, could we just pick the perfect way _after_ gathering enough samples and a pattern reveals itself? Let's just start gathering samples, and store them and their results in a fancy data structure that will allow us to stop as soon as _any_ way of splitting the space gives statistically different results for the two sub-regions. (This reminds me a bit of [support-vector machines](https://en.wikipedia.org/wiki/Support_vector_machine#Definition))

Seems like a tall order, but if we represent the input to our functions as _bit vectors_ then it becomes doable. In our Mt. Everest finding example, the function being optimized takes 2 floating point numbers (lattitude and longitude) so the bit vector equivalent would be the 128 bits corresponding to those two 64 bit numbers put back to back.

Here's the algorithm:

1. Begin sampling points from the current search region, placing them into a set ordered by their output on the function being optimized. After each sample, examine the top 50% of the samples - call these the "winners". If among the winners, any bit position is mostly 1s, we fix that position to be 1 for subsequent sampling. Likewise if any position is mostly 0s. To be more precise: we will only lock in a bit if there's a statistically significant difference in the number of 1s vs 0s for that position.
2. By locking in bits, this constrains the search to a sub-region of the original space. We're back to step __1__, but with a smaller search space.

__Aside:__ I suspect you can use a finger tree with an interesting monoid to make it logarithmic time to determining if there's a bit position that can be fixed.

So this algorithm learns one bit at a time of an answer, in whatever order is easiest, eventually converging to having all the bits set. It uses only the statistics of different regions of the space. Interestingly, the order of the bits is irrelevant: if we reversed the 128 bits of our input or interleaved the two 64 bit numbers everything works the same.

For our running example of finding the top of Mt Everest, it will learn the more significant bits, which roughly pin down regions with higher than average elevation, then learn less significant bits.

## Algorithm 4

We can simplify Algorithm 3. No fancy data structures needed.

1. Start with a collection of P bit vectors. (“P” for population size) To insert a bit vector, b, into the population, add it to the population, then evict the bit vector with the worst fitness (might be 'b' itself).
2. At each step, generate a new bit vector by picking a bit at each index in proportion to the current distribution of bits for that index in the population. For instance, if the first index is 80% 1s in the population, you select 1 for that index with 80% probability (you can make it more aggressive with a nonlinear function here). Then insert that bit vector into the population as in 0, possibly displacing another member of the population or just evicting itself if its fitness is worse than everything already in the population.
3. That's it, just repeat for as many steps as you like.

This algorithm might not seem all that similar to Algorithm 3, but let's imagine applying it to our Mt. Everest example. Initially, the algorithm just picks points randomly. After the number of samples exceeds the population count (say, P=1000), it begins discarding samples with lower elevation. Among the samples retained, they may have some bit positions in common. Suppose it's the bits that indicate "northern hemisphere". As a result of this pattern, the algorithm does more sampling in the northern hemisphere, which will (perhaps) find more high elevation points in the northern hemisphere, which further increases the weight of those bits. Eventually, those bits are effectively locked in and search proceeds on other remaining unfixed bits.

## Surprise!

The algorithms discussed here use the same principles as the [genetic algorithm](https://en.wikipedia.org/wiki/Genetic_algorithm) (GA), which are believed to work via this process of progressively locking in bits of the solution. See [this paper by Burjorjee on "hyperclimbing" in genetic algorithms](https://arxiv.org/abs/1204.3436).

Also see:

* [Population-based incremental learning](https://en.wikipedia.org/wiki/Population-based_incremental_learning) which is very similar to my Algorithm 4 above.
* [The compact genetic algorithm](https://pdfs.semanticscholar.org/4b0b/5733894ffc0b2968ddaab15d61751b87847a.pdf), which doesn't require keeping a full population of bit vectors in memory, only the % of 1 (or 0) bits at each bit position. This makes it practical even for large models.

In the compact GA, only the % of 1 (or 0) bits at each position is retained (this is called the "probability vector"). The training looks like:

1. Generate two models, s1 and s2, given the current probability vector.
2. Compare s1 and s2 against the objective function to determine a winner. Move the probabilities some small step toward the winning vector.
3. Repeat.

One further optimization not mentioned in the paper is that for supervised learning where the objective function is an average of performance over a large training set, comparing two models can be done more efficiently: rather than running each model against the whole training set, instead feed the training set to each model incrementally, stopping as soon as one model is statistically better than the other. And in general, there's more you can do to be sure you make the most of each evaluation--you don't want to spend a lot of evaluations on comparing two models which are quite similar in performance, when it's very easy to find two models in the remaining search space that are quite different in performance.

## Discussion 

Could these gradient-free learning algorithms be used for neural network training and other forms of supervised learning? It's quite nice that they don't require any fancy frameworks or differentiability, just a way of representing the function's parameters as a bit vector, which isn't exactly hard.

While there's been some use of GA-style algorithms for supervised learning (though it's typically for hyperparameter search), the field seems pretty centralized: a few organizations with access to massive compute resources have picked a standard set of techniques and frameworks, and they get the most impressive results. We may not really know what techniques work best, but because some orgs can throw 100x more compute at the problem using their preferred techniques, they get better results, and the techniques they use thus attract more mindshare.

Next time we will look at when GA-style search does well and consider a new class of adaptive learning algorithm which _[figures out how to make a problem learnable by GA-style search](/2019-10-07/learnability.html)_.
