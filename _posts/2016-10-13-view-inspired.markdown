---
layout: post
categories: [tech, programming]
title: The design failures of view-first
---

This post advocates a design philosophy I'm calling _view-inspired, model-driven_. We'll look at a domain, task-tracking and project management, and examine the failure of an alternate philosophy, 'view-first', using Trello as the case study. I don't mean to pick on Trello specifically, because once you are on the lookout for it, you can see the symptoms of view-first design in many applications.

### Task tracking

Good task tracking and project management is based on 1 important principle:

_By default, the user should only have to look at the tasks that are __currently__ relevant to them._

This sounds kind of obvious, but it's amazing how poorly existing productivity and task-tracking apps facilitate this. More on that later.

A task might not be relevant now because:

* It's blocked on another task or it depends on another task which isn't done yet. ("Publish website" comes after "purchase domain for 
site")
* It can't or shouldn't be addressed until some time in the future. ("Give Bob a call on Friday")
* It's a task assigned to someone else ("@Alice do writeup of project 
proposal"), and the user isn't following up on this task until later.
* The user is at work, and tasks that can only be completed at home. ("Hang picture frame in living room")
* The task has decayed, meaning the preconditions for it being relevant no longer hold. ("Use the $15 off coupon for this restaurant before it expires next Tuesday")
* The task would otherwise be relevant, but has a lower priority and so isn't shown when the user has too many other higher priority tasks that are relevant.

_Note:_ We can view all these as a special case of a more general principle. Each task has what we'll call a *relevance function*. Given various inputs, the relevance function indicates whether the task is currently relevant. "Hang picture frame in living room" might have a boolean input for "whether the user is at home".

More technically, we want both perfect *precision* (only relevant tasks are shown) and perfect *recall* (all such relevant tasks are shown; none are left out). With bad recall, the user isn't shown and forgets to do an important task. With bad precision, the user is overwhelmed by lots of irrelevant info.

Poor recall is pretty obviously a problem, but some very bad user adaptations happen with poor precision:

* To deal with the bad precision, the user spends lots of extra time processing the information in the app. (Example: email, for people who maintain a strict inbox 0 discipline) This is sort of a best-case scenario. Most people aren't so disciplined.
* Instead, the user "adapts" by missing relevant information, since it's mixed in with too much extra info the user doesn't care about. (Again: email)
* The user also begins losing trust in the system and avoids adding items to it in a comprehensive fashion. This might improve precision, but loses recall. The user then faces anxiety due to a vague sense that tasks exist that aren't really tracked anywhere.
* The user stops checking the system, due to lack of trust in it. This is a downward spiral. As the user stops checking the system, the information in it becomes increasingly stale (precision and recall both decline).
* The user starts adding items to a different system. Sometimes items are spread across different systems, leading to additional processing time and anxiety about whether all items are tracked or whether you've checked all the right places.

If you've ever tried to use a task-management system and gave up after a while, maybe you recognize some of these failure modes.

### Case study: Trello

Trello is a nicely polished app billed as being capable of helping you "organize anything". In Trello, there are "boards", "lists" (within boards), "cards" (within lists), and "sub-card lists" (within cards). Because of this rigid hierarchy of items (a card cannot exist on two boards), attempts to achieve good precision and recall are doomed. The essential problem is that:

* Even for a single individual, a rigid hierarchy is inappropriate for managing the set of tasks in their universe. If I'm at work, I shouldn't have to look at tasks that can only be completed at home. If I'm working on project A, I shouldn't have to look at tasks relevant to project B.
* BUT, at the start of my day, perhaps I would like to view a summary of "upcoming tasks" for each of the different projects I'm involved with, to decide which project to focus on. Once focused on a project, more tasks and detail can be revealed, since the user has opted in to getting this information. That is, the user's notion of "what's relevant" is a very fluid notion, not something that fits in a fixed hierarchy.
* When multiple individuals are collaborating on items, a fixed hierarchy is even less appropriate, since it requires that everyone be in sync on what is relevant.

To combat these limitations, Trello has some support for "labels" and "filters". But bolting on features in an ad hoc fashion to compensate for a poor underlying model never works out nicely.

Let's look at a case study. Suppose Carol and Dave are a young, conscientious couple looking to get organized with all the tasks in their life. Dave is a graphic/web designer and blogger and has his own consulting business, while Carol is an attorney at a small 4-person law practice. Both Carol and Dave have lots of tasks and items they'd like to track that aren't relevant to the other. Dave might have various sales leads that he's tracking, workstreams for different active clients, random design ideas, blog post ideas, and so on. And Carol and the partners at her firm have info about active clients and their cases, and lots of other items that aren't relevant to Dave (and indeed, Dave should not be *allowed* to even see some of this information that is client-specific).

At the same time, Carol and Dave have lots of tasks that are common to both of them. There's day-to-day errands and tasks ("go grocery shopping", "buy new shoes"), but perhaps Carol and Dave are in the process of finding and purchasing a home. There's lots of tasks here, related to getting a mortgage, looking at homes, making offers, and eventually moving. Although most of these tasks can be anticipated in advance, there are a lot of dependencies between the various tasks and only a few can be actively worked on at a time.

How might Carol and Dave use Trello?

* Sharing a single board is a nonstarter. There's way too much information being tracked, and the tools for filtering and so forth are too limited. An essential problem is that Trello has no notion of task dependencies, and can't show you a view which filters out any tasks with uncompleted dependencies. People sometimes fake it by adding info or sublists to a card, but these have second-class status and are quite limited (a sublist can only be one level, for instance).
* A problem with using card sublists to track dependencies is that Carol and Dave may have different ideas about whether they want an "exploded" or collapsed view of a task and its dependencies. Dave may be fully-owning some portion of the house-buying process, like obtaining a mortage, and hence may want to view dependent tasks as separate cards with more detail and notes, while Carol may be comfortable with just seeing the overall "obtain mortgage" item, if at all. But even for Dave, he may change his preferences about what he wants to see expanded. Perhaps he expands something into a card, then realizes later that the card can't be completed until later. There is very poor support for going the other direction (from cards back to sublist).
* Furthermore, Dave and Carol really *shouldn't* have access to information about their respective clients, which may be of a sensitive nature.
* They might try splitting things out into different boards. Perhaps a board for "Moving stuff", a board for "Mortgage stuff", a board for "Dave client leads", a board for "Dave active clients", a board for "Carol law firm clients", and so on. The problem here is that there's no longer a single place that either Carol or Dave can go to get a high-level view of each of these areas, with just enough detail for them to do planning or decide on an area of focus. Trello provides some ad hoc views, so Carol can view all tasks assigned to her, or all tasks that are overdue, but a) these views cannot even be bookmarked or saved, and aren't first-class in any way, and b) they are too limited in their capabilities. For instance, here are two extremely useful queries that Trello cannot express:
  * "Show me all tasks assigned to me without any dependencies" gives a list of all possible tasks that can be actively worked on now. Useful for planning a day out. May be further filtered by label.
  * "Show me all tasks assigned to others without any dependents" gives a summary of what others are working on. For instance, the "obtain mortgage" task might have lots of dependencies, but no dependents, since it is a "root" task. (Or it might be a dependency of the "move to new house" task if Carol and Dave choose to model it that way, and filtering could be done on this basis also.)

With great discipline and lots of processing time, people who are really committed to using Trello can probably find a workflow that gets them 85% of the way to a fluent, usable system. Then again, with discipline and lots of processing time, most people can also maintain inbox 0.

Trello isn't a bad application. It's polished, pleasant to use, and works quite well in some contexts. But it's based on a bad model, the result of lazily importing some physical-world metaphor into the design of software. It has a surface-level intuitiveness and ease of use, but breaks down for more complex scenarios.

We are seeing here the deficiencies of a "view-first" design philosophy. The application was designed around a particular view. The view is polished and pleasant for what it is, but other views, or even more dynamic views, are just not well supported because the underlying model is too limited.

I advocate a design style I call "view-inspired; model-driven". It's fine to use views as inspiration for an application, since we are often driven to design applications by thinking of a possible end user experience. But these ideas should just be taken as inspiration to drive development of a clean, general model that can express both the views you think of now *and the ones you (or your users) might think of later*.

### A model for a task management system

I hesitate to call this a model for a task management system, because it's actually something more general-purpose. But it can certainly be used for task management. The model is simple and consists of two parts.

First, we have a set of *cards*. I'm not calling the cards "tasks" since a card might be used for other things (ideas, notes, whatever). Each card has a set of *fields*. Here are a few example fields:

* `due-date`, of type `Date`, indicating when the card should be completed
* `archived`: a `Boolean` indicating whether the card is 'archived' or not
* `description`: a `String`
* `dependencies`: a `List Card`, indicating any dependencies
* `labels`: a `List String`, with some labels assigned to the card
* `assigned-to`: a `List User` with some users assigned to the card
* `readable-by`: a `List User` indicating what users may view the card. The 'Public' user means anyone, without needing a login.
* `writable-by`: a `List User` indicating what users may view and edit the card

Not all cards must have the same fields, and in fact my comments on the above fields are just comments or conventions. With the exception of `readable-by` and `writable-by`, no fields are magic or built-in. The meaning of these fields is derived by the *queries* used to build views of this set of cards that constitutes the model.

The other part of the model is a list of *views*. A view denotes a `Set Card -> List Card` (we might call this the "query"), plus a `Card -> Card` indicating how cards created within that view should be initialized. Perhaps a view also includes a *rendering function* to be used for each card returned by the query. The general idea here is the view converts a set of cards to an ordered list of cards, chooses how each card is displayed, and also controls how cards created within that view are initialized. Views are first-class. A user saves these views to their account, but can also share them with others. (Unlike every other app for task tracking that comes with a rigid set of predefined views.)

I won't go into detail on the query language, but it should support common queries like:

* All cards with a label "House stuff", ordered by priority
* All cards due in the next week
* All cards assigned to Alice or Bob
* Etc

Note that when creating cards, we may do so in the context of some view. Any labels, due dates, or other fields selected by that view should by default be included in any newly created cards, and the user may opt out of these defaults if they wish. So if I have a view which is "tasks assigned to me due this week", and I create a card within this view, it should be initialized with a field that assigns the card to me, and the default due date for the view might be Friday of the current week. 

By not special casing things, we get a more powerful, flexible system that is simultaneously simpler to implement. Note that with this model we can *compose* views, basically adding subfiltering. 

That's it for the model. I'll leave it as an exercise to figure out a nice UX for creating and interacting with this model.

### Remarks

We often think about views first because views are concrete, and it's what we interact with directly when we use software. But actually designing software 'view first' is problematic because it leads to rigid models that aren't flexible enough to support the myriad of creative ways that people use your software. It also leads invariably to feature creep---when your model is overly influenced by some concrete views you had in mind during design, it invariably ends up insufficiently general purpose. So as your software becomes more popular, you start adding one-off 'features' to support concrete use cases that your users are asking for. A few years pass of this feature creep, and you have a bloated, complicated piece of software that no one gets joy out of using.

On the other hand, 'view-inspired; model-driven' means picking a general-purpose model, with a rich algebra, right from the start, and then crafting views (or letting users craft views) that fetch data from and allow various interactions with the model. There are no 'features' to add, no feature creep, and the implementation can be much much simpler.
