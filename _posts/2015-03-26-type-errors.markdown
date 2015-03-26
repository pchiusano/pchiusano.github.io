---
layout : post
title : I hate type errors!
categories : [fp, unison]
---

I hate type errors!

Even though the Unison editor isn't yet suitable for real programming, my experience using it has made me even more aware of just how much time programmers waste dealing with type errors:

* Let's say 95% of errors are trivial to fix. You probably don't even bother reading the message. A quick look at the line number and ten seconds of thinking and you know the fix. Flow is maintained. Your focus stays on what matters---making the actual edits to the code. You've made an edit, and the compiler has helpfully pointed out a missed or incorrect step, which you immediately rectify. This is why people like type systems.
* In 5% of the cases, the error message(s) are misleading and/or point to the wrong location(s). You take a look, scratch your head. 30 seconds go by; it becomes clear you aren't going to intuit this one. Now you actually have to gather more information. Okay, maybe you'll start by reading that error message more carefully. Is the information useful? Maybe. Maybe you try a change, use typed holes to gather info about the types of values in scope, etc. But recognize that you are now in full-on yak-shaving mode. You started off by conceiving of some edits to your code. You then made these edits, but now look. You aren't productively fixing your mistakes or providing missing steps, you are engaged in _a very inefficient search process to narrow down and understand what the problems even are._

Note that if we deferred our typing errors until runtime as in a dynamic language, we wouldn't have to spend _any_ time deciphering type errors, and the runtime errors would now be in terms of actual program values that we can inspect, rather than a possibly inaccurate _symbolic description_ of the problem. Of course I feel this is throwing the baby out with the bath water---static types are overall a huge win especially in languages like Haskell with nice type systems. But I point this out because I think it's important to develop some perspective.

When you start out doing typeful programming, perhaps that 95% is more like 60%. A very large number of error messages you get from the compiler are frustratingly opaque. What the hell is the compiler even talking about? Many people in this situation get frustrated enough that they leave the typeful programming world in favor of some dynamic language. But for those who stick with it, over time, with experience, you get better at intuiting what the issues are or avoiding them in the first place, and the 60% creeps up to 90%, 95%, 96%, 97%...  But here is the problem: in getting to this point, you have brought the cost of deciphering errors down to acceptable levels, but also lost perspective on the cumulative costs. The past costs are forgotten and the present costs are ingrained in your workflow and thinking, a productivity tax you don't even really notice or think about.

_Aside:_ I consider this a problem with our industry and education system; we are raising generations of programmers implicitly taught to accept everything as a _given_, no matter how arcane or costly. But in software, nothing is a given, every single aspect of the software world is the result of [_choices_ made by people "no smarter than you"](http://www.gurteen.com/gurteen/gurteen.nsf/id/no-smarter-than-you).

But even for people with experience, the costs of deciphering type errors is much higher than that 5% would indicate. The issue is not the _direct_ costs due to time wasted deciphering type errors, it's the _indirect_ costs.

Programming is an activity that demands focus. When focus is maintained, it is possible to accomplish tasks in hours that might otherwise take weeks. Maintaining a state of flow for longer periods of time is an almost trancelike experience, and incredibly empowering. One feels attuned to the tasks at hand, subtasks get pushed and popped from the stack, and there is a feeling of directness, control, and creative energy.

But maintaining this level of focus is rare. We don't like to talk about it, but if we are being appropriately humble about our limitations, we must recognize that human focus is a very scarce and very fragile resource. Our puny little brains can only allocate so many resources, and us programmers are constantly losing focus. I don't mean obvious distractions like wasting time checking Twitter or Reddit when you should be doing something. These things are often the symptom, rather than the disease. What I mean is the act of doing things that _aren't the actions most likely to be most productive at furthing progress_. Everyone knows what that feels like. Perhaps you are debugging, and rather than methodically narrowing down the problem via a set of well-chosen experiments each giving maximal information, you instead meander around, perhaps adding random print statements or inspecting random values in the debugger. When you should step back and go for a walk perhaps, you instead stare at the screen. You've lost focus, but before you realize it and do something productive about it, you've lost an hour.

Deciphering type errors leads to exactly this same sort of inefficient, meandering, unfocused mode of work, perhaps on a smaller scale. But the costs are still there, and they are substantial, if you pay attention.

Also see: [Why are we still programming like it's the punchcard era?](/2014-09-30/punchcard-era.html)
