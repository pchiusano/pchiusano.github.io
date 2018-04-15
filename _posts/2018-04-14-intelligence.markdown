---
layout: post
categories: [science]
title: The Fermi paradox, and is "intelligence" the inevitable byproduct of any long-running evolutionary process?
---

I read a [fascinating book recently](https://www.amazon.com/Universe-Teeming-Aliens-WHERE-EVERYBODY/dp/3319132350/ref=dp_ob_title_bk) on the [Fermi Paradox](https://en.wikipedia.org/wiki/Fermi_paradox), which asks the question: if there are supposedly millions of other civilizations in the galaxy, why haven't we detected any real evidence of their existence? One class of solutions says: what happened on Earth is a fluke. We're alone in the galaxy. Gosh, that would be disappointing! Let's have a closer look at one piece of the puzzle.

One of the terms of [the Drake equation](https://en.wikipedia.org/wiki/Drake_equation) is "the probability that _intelligent_ life arises on a planet where life exists". Put another way, does "intelligence", however defined, have any special status in evolution or is it just like any other adaptive trait---no different in principle than, say, spiders evolving to spin webs or fish being able to swim? If it's nothing special, then maybe it is a fluke that intelligence arose on Earth. 

Here's a simple argument for why evolutionary processes will, if they avoid getting stuck (more on that later), develop toward increasing levels of intelligence:

1. Any evolutionary system (defined as a system where entities reproduce with variation and are subject to natural selection) undergoes change, often due to _the evolving behavior of other organisms_. For instance, when a predator evolves new behaviors, the selection pressure on its prey changes.
2. Thus, speed in adapting to change is selected for, as is the scope of adaptations that can be made by species (I'll call this "adaptability"). A species that can respond to change only over the course of eons is at a disadvantage to one that can respond even just a little more quickly. (Example: an organism with only hardwired evolved behaviors cannot respond in time to new behaviors evolved by one of its predators, and is less fit than an organism that can do even a tiny amount of learning to adapt to its predators)
3. Over evolutionary timescales, we therefore see species evolve an increasing ability to adapt to their environments more quickly. _This generates a positive feedback loop_---when one species develops an ability to adapt more quickly, not only does this benefit that species, it also results in an environment which changes even more quickly, generating further selection pressure on other species to gain more rapid and effective means of adapting to changes.
4. The long-term trend of this positive feedback loop is what we now call "intelligence". Intelligence isn't about any particular skill, it's about the ability to adapt to an ever-expanding scope of possible changes on shorter and shorter timescales. Provided evolution does not get stuck, intelligence is the result.

Let's look at how this has played out in biological evolution:

* The first single-celled organisms were immobile, at the mercy of whatever the conditions of the drop of water they found themselves in. The evolution of _motility_ allowed an organism to adapt more readily to a changing environment---it could move to new food sources, away from predators, etc.
* Vision and other forms of sensing served a similar purpose---providing an organism with information it could use to adapt to change on shorter timescales. But with sensory input comes a need to have some ability to _perform computation_ on that sensory input and change behavior in response to that input.
* Having an ability to _learn_ new behaviors, rather than merely having the function from sensory input to behavior be hard-wired was a further improvement in the speed and scope of adaptability. Any organism with even a modicum of ability to learn could be at an advantage.
* The logical endpoint of this process is investment in a general ability to sense, learn, and implement new behaviors (what we now call "intelligence"), which can respond to change much more rapidly than the eons required to implement changes to the genetic code.
* Language and culture is a further improvement in adaptability---it allows for a thing to be learned by just one organism, shared on very short timescales with other members of the species, and retained across generations (rather than being forgotten and relearned by each generation). Written language is a further amplifier of this effect.
* In general, cultural and technological evolution increase the adaptability of a species much more efficiently than the slow, generational process of evolving the genetic code.
* And so on...

A spider which has evolved over millions of years to be able to spin webs is impressive. But it's in possession of a specialized piece of hardware. Over time, evolution discovers that a good survival strategy in a changing environment is _less_ specialized, more general purpose hardware on which many pieces of software (learned behaviors) can all run.

## Getting stuck

Even if growing intelligence is a trend in evolutionary systems, it's very possible for evolution to get stuck in equilibria. This might not be obvious, especially if you read popular accounts of evolution where it's easy to get the impression we have it all figured out.

To see some of the problems, consider a simple island ecosystem with only plants (who feed off sunlight), deer (who eat the plants), and wolves (who eat deer). Even if deer can in theory become more fit by becoming more capable at evading wolves (perhaps by learning to better predict wolf behavior), who is to say that such adaptations are "reachable" according to the variability available to deer? (Deer are not going to develop opposable thumbs, large brains, language, and toolmaking all in one go, and likewise for wolves.)

Other pathologies: the wolves might evolve to be so good at hunting deer that they decimate the deer populaton (which they are dependent on). With the primary food source of wolves decimated, adeptness in hunting deer is no longer selected for, and if the wolf population repopulates, who knows what adaptive traits remain (selection may shift to favoring wolves capable of going longer without food, for instance). Likewise, if deer become so good at evading wolves, the wolf population may decline and the deer may overgraze the plant population, and so on. The system can bounce cyclicly between different states, failing to make any "progress".

Is it just a fluke that biological evolution seems to have evolved in an open-ended fashion, or do certain kinds of evolutionary systems, like the evolution of earth's biosphere, have open-endedness as a near inevitability? And if so, how might we engineer such systems? This is a question studied in artificial life and evolutionary computation (which I studied a long time ago). At the time, no one seemed to understand these questions very well at all, and I'm curious if progress has been made.

The way I felt when studying evolutionary computation is that our understanding of evolution was roughly analogous to our understanding of flight before the invention of the airplane. We understand the basic principles at work. We suspect we could replicate the performance of evolution with an extremely detailed simulation of biological evolution (though even this is uncertain---there are questions about whether we'd end up with similar results on Earth if we just "replayed the tape"). But it isn't clear what aspects are truly essential or if we could substantially simplify our simulations and achieve similar results.

## Fermi

I don't know how likely it is that Earth's evolutionary process got as far as it did. I suspect there isn't anything special about it. Time and time again, when we look at biological evolution and wonder how it ever assembled some complicated piece of biological machinery, we learn it found a way (often rediscovering the [same complex structure over and over again](https://en.wikipedia.org/wiki/Evolution_of_the_eye)). Seeing this kind of thing makes me hesitant to conclude that it's a fluke evolution didn't get stuck at the prokaryote stage or someplace else along the way to general intelligence. Perhaps there are things about biological evolution that make it unlikely to get trapped in equilibria, which we don't fully understand yet?

I'll close with this quote by Richard Dawkins:

> Never say, and never take seriously anyone who says, "I cannot believe that so-and-so could have evolved by gradual selection". I have dubbed this kind of fallacy "the Argument from Personal Incredulity". Time and again, it has proven the prelude to an intellectual banana-skin experience.

I'd love to hear your thoughts on this.
