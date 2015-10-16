---
layout: post
categories: [tech]
title: How Twitter could easily address its rampant abuse problem
---

Twitter has a problem with abuse. Everyone knows this. I'll explain the core issue, and then explain one way to fix it. 

Here's the core issue:

_Anyone on Twitter can cause a tweet to arrive in my notifications, just by mentioning my Twitter handle. And related to this, anyone viewing my timeline or following me can see these tweets, assuming they are replies to tweets I've made._

So, if some hateful nutjob decides they don't like me, they can tweet at me, and I'll have to see it or at least know it's there, and people who expand the thread under each of my tweets can see it also. I haven't granted this person the capability to contact me, yet Twitter allows it by default. If a mob of hateful nutjobs get together to abuse someone via Twitter, there is no real way to prevent it. The person being abused can block individual accounts, can report individual accounts, but that's really unsatisfactory:

* Firstly, the person at whom abuse is being directed shouldn't have to read that stuff in the first place. Nor should they have to spend their time reading nasty messages and reporting them. Think about it---even if you have a thick skin and don't mind seeing a barrage of random death threats and hateful messages directed at you on a regular basis, is processing this garbage what you want to be spending your time doing? Of course not.
* Secondly, it's so easy to create new accounts in Twitter that banning individual accounts is like playing whack-a-mole, and if there's a mob or group of people intent on abuse, Twitter doesn't have much power to stop them.

All right, now here's a simple solution. I'll make a few tweaks to it below, but this is the basic idea:

_Twitter should expose a setting which prevents Tweets from "untrusted" people from arriving in my notification timeline, or being visible to people who follow me._

It would be up to each Twitter user to define their "trusted" set of people. Here are some suggestions:

* Everyone (the current default, and only option)
* People I follow
* People I follow, and people they follow
* People 3 follow hops away
* People I am friends with on Facebook
* People whose account has been active for more than 6 months and have more than 400 followers
* Etc

You get the idea. Rather than just blindly trusting everyone with the authority to write to your notification timeline (about as sensible as giving everyone on the planet your home address and a key to your house) you instead assign trust more explicitly.

Let's see how this plays out:

* Suppose Bob The Abuser decides he'd like to send an abusive message to Alice. Having experienced some abuse before, Alice's trust level is a bit more restricted (perhaps it is one of the options I gave above, 2 following hops away). Bob, or random sockpuppet accounts that Bob creates, are not trusted by Alice by default, so Bob has no way of contacting Alice.
* Likewise, if Carol is following Alice and expands any of her tweets, she won't see Bob's replies there, either. 
  * _Only_ if Carol is following Bob explicitly will she see any tweets that mention Alice in her _regular timeline_.
  * _Only_ if Carol is explicitly following Bob can she click on Bob's 'tweets and replies' section and see tweets that mention Alice explicitly 
* It is as Bob and his angry mob of abusers are communicating in a totally separate universe which has no way of reaching Alice or any of her followers.

I have a couple improvements to this scheme:

* Bob is notified in realtime that neither Alice nor her followers will see the Tweet (a simple check of the Twitter handles in the message being composed can do this). Some abusers derive satisfaction from simply _knowing that their abusive message was delivered_, even if they don't get a response. Better to notify them ahead of time that their actions really are pointless. Hopefully they just won't bother.
* Alice can appoint _delegates_ to view untrusted notifications. A delegate is just a Twitter account who Alice grants the authority to view notifications sent to Alice that Alice herself won't see due to her trust settings. That is, while Alice appreciates not having to see or process the abuse that might be directed at her, if it is occuring, she might like to have _somebody_ do something about it (maybe some loyal followers she trusts). The delegates can see these untrusted Tweets directed at Alice, and can process them (likely by reporting them to Twitter and/or appropriate authorities). If a delegate came across a credible threat to Alice, he/she could notify Alice.

Without the ability to communicate with their victims, Twitter is no longer an easy vector for abuse. I won't say that it's impossible, but the costs of perpetrating abuse have become higher, and it's easier to combat. If Twitter implemented something like this, I believe it would have a huge positive effect.

### Objections ###

Some people might object that one of the points of Twitter is that anyone can contact anyone else in a sort of free-for-all global conversation. And Twitter gets used for "activism" for that reason. If a politician does something awful, it can be a good thing that a "mob" of people can rise up on Twitter and make their voice heard. If that politician had his/her trust level turned way down, this sort of uprising can't happen as easily.

My response to this is that we should encourage public officials and the like to publicize their trust levels and generally keep them set in a way that lets them and everyone else see just about all communication directed at them. That is, the way to deal with politicians or public figures who don't want to engage with the public is with simple social pressure. It's simply expected that certain people have communication lines open with the public.

Another objection is that if this gets implemented, Twitter would get used less because fewer messages are being delivered and thus fewer conversations are taking place. To me this doesn't seem like a problem, since the messages that aren't being delivered are the messages that _users have explicitly indicated they don't want delivered_. If some good messages get missed, why is this such a bad thing? Unless you spend all your time on Twitter, you miss some good messages anyway because the signal to noise ratio is so low!

Furthermore, the fact that Twitter provides such a rampant abuse vector has a very chilling effect. People avoid joining the platform, speaking their mind, or participating in conversations out of concern that that they'll be exposed to nasty response.

But honestly, I don't really care whether this feature improves or hurts Twitter's bottom line. I think it's just the right thing to do.
