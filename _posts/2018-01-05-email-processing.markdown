---
layout: post
title: Some simple email filters to help eliminate email overload
categories: [tech]
---

On this blog I've mused about the problems with [snail mail](/2014-06-21/capability-based-snail-mail.html): anyone with knowledge of your address gets a lifetime ability to cause mail and packages to show up at your house, as many times as they want. Email has the same problem with virtual message delivery. The result is that our inboxes, both physical and virtual, are filled mostly with content whose delivery we never actually authorized. They are mostly noise, and we spend lots of time just _processing_ that noise because there is some amount of _signal_ that we do want to be aware of and respond to with higher fidelity.

What if it were different? _What if every email in your inbox was either a reply to a message you've sent, or was sent by a person you've explicitly authorized to send you a message?_ And what if every other message sent to your email skipped your inbox and was assigned to a low priority bin that you could look at more infrequently? But wait, there's more! Imagine if you had, at any time, the ability to _revoke_ the ability of anyone to sent messages direct to your inbox, without needing to create a new email account.

This is all possible with a few simple email filters. Here's what I did for a new email address I set up recently:

* All emails sent to my email address `paul@example.com` are automatically archived.
* All emails sent to `paul+q84@example.com` are tagged with the label "Main" (and also archived). The `+q84` suffix is randomly chosen. Call this your "trusted" email address.
* All emails containing the (randomly chosen) _reply authorization key_ `zY83jd91` are tagged with the label "Replies" (and also archived).
* All sent emails have a signature block containing the reply authorization key (and perhaps a "what's this" link to this post).

The workflow is now to check the "Main" label and the "Replies" label. _Your inbox is always empty._

Here's how it works: emails that are a reply to an email you've sent will contain your signature block and hence the reply authorization key, so they'll be tagged with the "Replies" label that you check regularly. Emails sent to your main address get automatically archived, so you don't see them unless you specifically search for them or you are searching for all unread messages (tip: set up a regular calendar appointment to check unread messages for anything important). You can treat these messages a bit like your Twitter feed... a river of mostly noise that you might want to scan occasionally for anything important or interesting. And emails sent to your "trusted" email address get delivered to your "Main" label. Only give the trusted email out to people you... trust.

You can now freely give away your `paul@example.com` email address - anything sent there which doesn't have your reply authorization key is not an email you've asked to receive.

Some variations:

* On the `+q84` randomly chosen suffix: you can introduce multiple suffixes which are assigned to different labels that you check at different frequencies. You can also "retire" an old suffix at any time. Make up a new suffix, say `+g203`, update your filter, and then selectively notify anyone who you want to have priority access to your main inbox. It's like getting a fresh email address without losing your history!
* If you aren't quite so geeky and want to make it less obvious, you can just build the "Replies" label based on any unique string that appears in your email signature.
* Finally, you can also introduce multiple reply authorization keys and filter them differently, though this is a bit less convenient as you can't just use the built-in email signature mechanism to make sure they are included in the email body - you'll have to start pasting them in manually. On the other hand, with some keyboard shortcuts or snippet-expansion in your email editor, maybe it isn't so bad.

Let me know in the comments if you decide to adopt some variation of this and how it goes!
