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
* Anyone may create a 'pipe', which acts as both a consumer and a producer. The identity pipe just republishes all suggested content with a set of consumers (public, a concrete set, etc), but an API allows for more interesting pipes that do filtering, transformation, etc. 
* Suggested consumers 

Here's an example of how this plays out:

* Joe Sixpack, an economics researcher, posts some kitten photos publicly. By publicly, we mean that he suggests the 'Public' pipe associated with his account and identified by some URI as the consumer for his post.
* Later, Joe posts an interesting econ paper, also to his public feed.
* Carol, a kitten lover, reshares the kitten photo post to a 'Joe-Sixpack-Kittens' pipe and/or an 'Chicago-School-Econ-kitten-photos' pipe.
* Joe, or anyone with access to the Joe Sixpack 'Public' pipe (anyone on the internet) reshares the econ paper post to a 'Joe-Sixpack-econ' pipe. It does not matter whether Joe creates this pipe or whether someone else does so.
* Alice, an econ grad student, subscribes to only the 'Joe-Sixpack-econ' feed, which she merges into her main newsfeed. Alice

The 'pipes' in this model function a bit like tags, but importantly, we don't (necessarily) rely on Joe to appropriately tag all his posts. Anyone with access to Joe's post may do this, and we can incorporate permissioning for access to the pipes.
