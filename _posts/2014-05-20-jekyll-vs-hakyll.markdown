---
layout: post
title: Jekyll vs. Hakyll and the curse of magic implicit configuration
categories: [fp, haskell]
---

This blog currently runs off GitHub pages and [Jekyll](http://jekyllrb.com/). It's convenient being able to produce posts directly in markdown and preview them in the browser, which I hope will encourage me to post more frequently. But Jekyll suffers from the curse of magic implicit configuration, in which control flow is obscured from the user and one must tweak rather unguessable configuration settings to achieve different effects.

Let me explain: I wanted to add a link to a description of my [consulting services](/consulting-services) to the sidebar. I wanted to write the description in markdown, and have this markdown be converted to HTML as part of site generation. Unfortunately, dumping a `consulting-services.markdown` file in the root of my site directory didn't do it. The file was copied to the site and served at the url `/consulting-services.markdown`, as plain text, but was not passed through the markdown generator.

Okay, so it appears that while Jekyll by default passes appropriately named files in the `_posts` directory through the markdown generator, files in the root directory don't get the same treatment. Jekyll has extensive documentation, but not quite at the right level for me to be able to quickly answer how to get the behavior I wanted. So I resorted to googling. From my cursory investigation, it appears the `_posts` directory really is special. If you want to use markdown in other posts, you have to jump through the following hoops:

* Create a `foo.markdown` file and place it in the `_includes` directory
* Then, [create a foo.html file in the root directory, and `include` a markdownified version `foo.markdown` in `foo.html`, using Jekyll's ad hoc templating language](http://wolfslittlestore.be/2013/10/rendering-markdown-in-jekyll/).

Here's what that second solution looks like: 

{% raw %}
    {% capture my-include %}{% include foo.markdown %}{% endcapture %}
    {{ my-include | markdownify }}
{% endraw %}

Good grief!

Perhaps someone with more expertise in Jekyll can suggest a better alternative, but let's take a step back. The problem here is that Jekyll site configuration hides control flow. The control flow is a fixed skeleton, and one magically overrides what happens at various stages using the `_config.yml` file. Prose documentation is a poor substitute for just being able to read the control flow directly from the code.

Let's now compare this to [Hakyll](http://jaspervdj.be/hakyll/), another static site generator. In Hakyll, the site description is a normal Haskell file, which is high level but which makes control flow totally explicit. Here's an example Hakyll site snippet, taken from [this example](https://github.com/Keruspe/blog/blob/master/hakyll.hs):

~~~ Haskell
    -- Compress CSS
    match "css/*" $ do
        route   idRoute
        compile compressCssCompiler

    -- Copy files and images
    match ("files/*" .||. "files/*/*" .||. "images/*" .||. "images/*/*") $ do
        route   idRoute
        compile copyFileCompiler

    -- Copy favicon, htaccess...
    match "data/*" $ do
        route   $ gsubRoute "data/" (const "")
        compile copyFileCompiler

    -- Render posts
    match "posts/*" $ do
        route   $ setExtension ".html"
        compile $ pandocCompiler
            >>= loadAndApplyTemplate "templates/post.html" (tagsCtx tags)
            >>= (externalizeUrls $ feedRoot feedConfiguration)
            >>= saveSnapshot "content"
            >>= (unExternalizeUrls $ feedRoot feedConfiguration)
            >>= loadAndApplyTemplate "templates/posts-js.html" (tagsCtx tags)
            >>= loadAndApplyTemplate "templates/default.html" (tagsCtx tags)
            >>= relativizeUrls
~~~

Now, I am not an expert in Hakyll, and I don't know all the 'nouns and verbs' being used in this snippet, but what I like is that the control flow is totally explicit, or at least explicit enough for my purposes. We are explicitly copying all the files in the `css/` directory, passing them through a CSS compressor, it would seem. And I'm guessing that `idRoute` specifies the destination in the generated site will also be `css/`. If I want to learn more about this or figure out how to specify a different destination, I imagine I can just look at the API docs for `route` and go from there. 

Likewise, while there is a lot of stuff happening in the `match posts/*` stanza, and I don't understand all of it, it's all normal Haskell functions, which have type signatures. If I want to figure out how to customize what happens at any particular stage, I just modify the control flow to be exactly what I want. I don't generally need to search through reams of documentation or google around.

When you write libraries, don't obscure the control flow--leave the user in control of that. If there are common control flow patterns you wish to encapsulate, expose higher order functions for users to call if they want. Don't go down the path of having a fixed control flow skeleton, with magic implicit configuration for tweaking this skeleton.

[Discuss on Twitter](https://twitter.com/pchiusano/status/468813321223680002)
