---
layout: post
categories: [fp, unison]
title: Unison update 4&#58; more editor interactions
---

Here's a video of the latest progress. Watch me write the expression `1 + 1`, then evaluate it!! Further explanation below.

<iframe src="/unison/unison-update4-movie.html" width="420" height="570" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

There are a number of additional editor interactions shown here:

* In the explorer, typing a space followed by an operator accepts the current completion and opens a fresh explorer with the operator prefilled. You can see this in the video, where `1 +` becomes `(+) 1 _`.
* In the explorer, typing two spaces accepts the current completion and moves the explorer to the right. You can see this in the video.
* Typing 's' _steps_ (linking + 1 beta reduction) the selected expression.
* Typing 'e' _evaluates_ the selected expression (to weak head normal form).
* Global search is now enabled by default in the explorer. In the video, I look up the `identity` function.
* Notice that the completions are aware of the admissible type. When we go to fill in the second argument to `+`, we see the fully saturated version of `(+)`, applied to two arguments, `_ + _`.

Not shown:

* Typing 'a' applies a function to the selected expression, so `42` becomes `_ 42`, and the focus moves to the `_`.
* Typing 'r' does one eta-reduction, so `x -> f x` becomes `f`.
* Terms which are ill-typed but which match the search query are displayed in the explorer along with their types (but are unselectable of course).
* Builtin functions for concatenting strings.
* Stepping works fine under lambdas. For instance, we can simplify the `1 + 1` in `x -> f (1 + 1)` to `x -> f 2`.

I think there's a lot more work that could be done to improve fluidity, but already, it feels pretty nice!

#### Remarks

When the explorer shows terms which are ill-typed but which match the query, this is the closest we get to a 'compile error' when writing code in Unison. When your compiler is run in batch mode, the question of what information to display in the compile errors is very important. What if we miss giving some information that's relevant to fixing the error? Thus compilers like GHC will often dump out a screenful of text for even very simple errors. There's also entire lines of research devoted to the question of how to minimize error messages, make them maximally informative (see type error slicing), and so on.

In Unison, we don't need to decide what information is relevant in an "error message" because the user can explore their program interactively. You are shown the type of your search result, the admissible type, the current type, and the types of all local variables, which is often enough to make it clear how to modify your code. But if you desire more information, you can navigate around to get the types of any related expressions you care about, and this can be done very quickly. The question of 'what information to display in the compile error' becomes irrelevant! There are no compile errors, and the user can extract the information they deem relevant _interactively_.

Here's a question: should the name of the identity function be `id` or `identity`? Maybe you have some opinions about this. In Unison, we can actually store _both_ names in the metadata associated with the term, and the search will return a term if any of the names match the search query. Regardless of our choice, it's not an additional _typing_ burden to have a longer name---the programmer can type `id` followed by two spaces to accept `identity` (or `id`, `<enter>`, etc). Name resolution is always type-directed, and the user specifies the minimum information necessary to disambiguate.

In working on Unison, I've found it's very interesting to think about the distinction between a _language feature_ vs an _editor feature_. We often conflate the two. The syntax for "imports", name resolution, etc, are all considered part of the language. In a semantic editor, many of these concerns are better dealt with by the editor, which can incidentally be much more flexible (allowing multiple names for a given term is just one instance of this). Raw text program editing has been holding us back!

That's all for now, more updates soon!
