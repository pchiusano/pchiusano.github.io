---
layout: post
title: Consumer-side filtering conspicuously missing from Facebook and Google Plus
categories: [ux, tech]
---

My daughter Arianna was born just over two weeks ago. Like any new father, I was excited to share some photos of her with friends and family... but I found I wasn't totally happy with any of the sharing options available to me on sites like Facebook and Google Plus. Here's the problem: I have a set of people who I'm comfortable sharing these photos with. Both Facebook and G+ allow me to create sets of users (which G+ for some reason calls circles), and for any content I _produce_, I may select which set of _consumers_ I wish to grant access to this content, which might be 'everyone', a concrete set like `{bob, dave, carol}`, or some other suitably defined set like 'friends of my friends'. Neither FB nor G+ support the obvious feature of nesting these sets, a feature supported by email lists for the past 20 years, but let's ignore that misstep for now. As the _producer_, I get fine-grained control over who I wish to share my content with.

Consumers, on the other hand, get only the most coarse-grained control over the information that is shared with them by producers. As a consumer on G+ or FB, the filtering I get is at the level of a user--I can choose to follow some user or apply some blanket rate-limiting policy to all the content produced by that user. Conspicuously missing is the ability for consumers to opt out of particular types of posts--just because I'm comfortable sharing pictures of my family with group X doesn't mean all the people in X who follow me on these services care. Perhaps they only care about my posts on functional programming, and yet I am now dumping photos of my family into their news feed. I've been on the consumer side of this, and it's pretty annoying. 

There are a number of people I don't know personally but who I follow on Google Plus because they produce some interesting content that I find relevant related to functional programming. Unfortunately, some of these people also post lots of content *publicly* I don't care about--videos of their kids, cute kitten photos, whatever. Wouldn't it be great if I could selectively subscribe to only the posts I considered relevant?

__Bob:__ Oh, come on, do you really care that much about having a perfectly curated news feed? Be honest, do you even read all the posts in your feed so carefully? It's just a 'river of news' model, and you aren't expected to exhaustively consume it.

__Alice:__ Yes, you're right, when you have a information feed with a bad signal to noise ratio, skimming it at random is a pretty reasonable strategy, because exhaustively reading it would be a waste of time. Lack of consumer-side filtering has *caused* the reading patterns you're referring to. The question is, why don't we have a better system that lets us construct feeds with better signal to noise?

__Bob:__ That sounds like it could be complicated. You really think users are going to understand anything so advanced?

__Alice:__ So what? It's the job of software, and UX, to make a more useful model approachable and usable. We shouldn't just settle for an inferior model because we're too lazy to come up with a good UX for it. 

Here is a nicer model for information sharing, allowing for both producer-controlled limited sharing, and consumer-side filtering, transformation, and annotation. Separate from this, we can figure out a good UX for this model:

* A producer of content selects a set of 'suggested consumers' for each piece of content. This content is made available to each of these consumers. In this model, producers do not _send_ content to consumers, they _suggest_ it.
* Anyone may create a 'junction', which acts as both a consumer and a producer. The junction has a set of producers that it 'follows' and a set of consumers that it suggests. The identity junction follows 'All' and just republishes all content that any producers suggests to its output set of consumers (public, a concrete set, etc), but an API allows for more interesting junctions that do filtering, transformation, etc.
* Anyone granted access to content, via a suggested consumption, may choose to republish it to one or more consumers.

Here's an example of how this plays out:

* Joe Sixpack, an economics researcher, posts some kitten photos publicly. By publicly, we mean that he suggests the 'Public' junction associated with his account and identified by some URI as the consumer for his post.
* Later, Joe posts an interesting econ paper, also to his public junction.
* Carol, a kitten lover, reshares the kitten photo post to a 'Joe-Sixpack-Kittens' junction and/or an 'Chicago-School-Econ-kitten-photos' junction.
* Joe, or anyone with access to the Joe Sixpack 'Public' junction (anyone on the internet) reshares the econ paper post to a 'Joe-Sixpack-econ' junction. It does not matter whether Joe creates this junction or whether someone else does so.
* Alice, an econ grad student, subscribes to only the output of the 'Joe-Sixpack-econ' junction, which she merges into her main newsfeed. Alice is spared having to sift through the kitten photos posted by Joe in her feed. Alice is also not forced into making an 'all-or-nothing' follow/no follow decision based on her perceived signal to noise ratio of Joe's public feed.

The 'junctions' in this model function a bit like tags, but importantly, we don't (necessarily) rely on Joe to appropriately tag all his posts. Any node in the network of junctions with access to Joe's post may do this, and the permissioning on the input side of junctions lets us construct public feeds, carefully curated restricted feeds, and everything in between.

I believe it's fully possible to make a very nice UX for this model, and it's both simpler and more powerful than the model backing services like G+ and Facebook.

[Discuss on Twitter](https://twitter.com/pchiusano/status/468483806467719169)
