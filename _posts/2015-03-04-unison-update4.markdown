---
layout: post
categories: [fp, unison]
title: Unison update 4&#58; more editor interactions
---

Here's a video of the latest progress. Watch me write the expression `1 + 1`, then evaluate it!! There are a number of things happening here:

<iframe src="/unison/unison-update4-movie.html" width="400" height="570" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

There are a number of additional editor interactions shown here:

* In the explorer, typing a space followed by an operator accepts the current completion and opens a fresh explorer with the operator prefilled. You can see this in the video, where `1 +` becomes `(+) 1 _`.
* In the explorer, typing two spaces accepts the current completion and moves the explorer to the right. You can see this in the video.
* Typing 's' steps (linking + 1 beta reduction) the selected expression.
* Typing 'e' evaluates the selected expression (to weak head normal form).
* Global search is now enabled by default in the explorer. In the video, I look up the `identity` function.
* Notice that the completions are aware of the admissible type. When we go to fill in the second argument to `+`, we see the fully saturated version of `(+)`

Not shown:

* Typing 'a' applies a function to the selected expression, so `42` becomes `_ 42`, and the focus moves to the `_`.
* Typing 'r' does one eta-reduction, so `\x -> f x` becomes `f`.
* Terms which are ill-typed but which match the search query are displayed in the explorer.
* Builtin functions for concatenting strings.
* Stepping works fine under lambdas, and in terms with free variables. For instance, we can simplify the `1 + 1` in `x -> f (1 + 1)`.

I think there's a lot more work that could be done to improve fluidity, but already, it feels pretty nice!

Here's a question: should the name of the identity function be `id` or `identity`? Maybe you have some opinions about this. In Unison, we can actually store _both_ names in the metadata associated with the term, and the search will return a term if any of the names match the search query. Regardless of our choice, it's not an additional _typing_ burden to have a longer name---the programmer can type `id` followed by two spaces to accept `identity` (or `id`, `<enter>`, etc). Name resolution is always type-directed, and the user specifies the minimum information necessary to disambiguate.

That's all for now, more updates soon!
