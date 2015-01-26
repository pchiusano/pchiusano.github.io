---
layout: post
categories: [fp, unison]
title: Taking a break to focus on Unison
---

I've decided to do something crazy and take a three month break from any consulting work to focus on getting [Unison](/unison.html) off the ground and open sourced. For a while, Unison has been a fun side project, but I've wanted to give it a little more love and dedication and see where it goes. My wife and I talked it over recently and decided to timebox the crazy to three months, and I've also notified my clients and am wrapping up any loose ends this week. Though it would be great to find a sustainable source of funding for the project, for now I'm planning on resuming regular consulting work at the start of May, at which point Unison will revert to side project status.

I'm very much looking forward to sharing updates with you here, and I'll open source the work as soon as it reaches a non-embarrassing state. In an effort to hold myself accountable I'd like to give a tentative roadmap here and also commit to doing at least one Unison status update post per week. I hope it will be interesting for readers of this blog to follow along!

Also, I'd be interested to hear from folks in the comments who have attempted something similar---taking time off from "real" work to try working on something experimental and/or crazy. How did it go, what did you learn? Any tips, pointers, advice, or encouragement? This is new territory for me.

With that said, here's the tentative roadmap. The overall goal: get to a point where I can actually show a compelling demo that exercises all the Unison components. I'm calling this Milestone 0 (M0). It's more important to me to have the "story worked out" and have the minimum viable product (MVP) for each component. Details and sophistication for each of the components can be filled in later, and I'll only block progress on technical issues that have wide-ranging effects on overall architecture.

* _Feb:_ focus on the Unison editor, a browser-based semantic editor for editing, interacting with, and viewing Unison programs. A lot of this is implemented; a lot of it isn't yet. I'm expecting this to be most of the first month of work. I'm placing a lot of effort on it because it's the biggest unknown for me. But I'd rather reduce scope on the editor and have something working end-to-end by end of Feb. 
* _March:_ polish, prepare for open source. After getting a working end-to-end, take a step back, see what details really could use filling in for M0. I've punted on a lot of stuff, now's the time to circle back and fill in what is essential. Goal of adding polish, getting project open sourced by end of month.
* _April:_ show demos and get feedback from as many people as possible. Figure out what the next steps are for the project. Figure out where this project is going, if anywhere. Write a post-mortem about this three month experiment.

Looking forward to sharing updates. Stay tuned!

#### Addendum: what's already done ####

... at least to MVP status:

* System architecture: what the pieces are; how they fit together. 
* Unison node: this includes the core language, a very simple storage layer, and an HTTP interface to access the node API from the editor. The node API is designed mainly to support rich, fine-grained interaction with the editor, not batch-mode compilation.
