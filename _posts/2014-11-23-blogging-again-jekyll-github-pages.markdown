---
layout: post
title:  "Blogging Again: Jekyll on Github Pages"
date:   2014-11-23 13:00:00
---
For the past year I've been extremely blessed and fortunate to have a lot of work which means I've been extremely busy. A lot of extra things in my life have had to be put on the back burner and blogging is one of those things. Last week I start re-evaulating my priorities to try and be more efficient in my daily tasks and I realized that I really miss blogging. I've come up with a way to add it back in to my work load but that also means I needed to come up with a super efficient way to blog.

While probably the simplest approach, I despise Wordpress with a fiery passion. No matter the amount of tweaking I do or servers I throw it on, I cannot get it to perform the way I'd like. Writing posts via their interface is painful. I'm constently trying to tweak themes in a structure that no sane person could have imagined. And then there is plugin hell. Also, I've always wondered why blogs need to be stored in databases.

My "current" blog, and by current I mean the one I'm replacing with this, uses [Octopress][octopress]. I like Octopress for the most part but it still feels like too much. Then the other day I ran across Bruce Eckel's article on switching to Github Page from Artima. In his article he says

<blockquote>
There’s been a big movement in recent years to static site generators for blogs. These are more efficient (they only get served, not dynamically created) and safer (also because they are not dynamic). Much of the material on the internet doesn’t need to be dynamically generated, and blogs are a particularly good example.
</blockquote>

YES! I think I literally agreed out loud. In fact, I could just post a link to his blog and say `me too`. So yes, a big reason I'm switching to Github Pages (and Jekyll) is because I feel the same way that Bruce does. I've provided the links used to set everything up at the end of this post. Most are the same ones used by Bruce but I'll save you the level of indirection.

I am personally choosing Jekyll with Github Pages for a few reasons.

### Jekyll is dead simple
Jekyll is a blog-aware static site generator. You write your posts in Markdown and a build process generates the static HTML. The HTML and assets (css, javascript, etc) are then simply uploaded to a web server like Apache to be served. This makes your blog super fast; no databases and awesome caching.

### Clean, simple layout structure
I've chosen not to go the theme route and to keep things as simple as possible. I added the Bootstrap css library for a few goodies, but for the most part it simple HTML with some simple styles. Breaking the layout into different files is built in to Jekyll and it is easy to apply a different layout to posts vs pages. I prefer a minimalist design but it would be really easy to pimp this site out into something a lot more extravagant.

### Markdown wins over WYSIWYG
When I'm writing my posts, I don't want to spend time moving my hand from the keyboard to the mouse just to highlight and click some **bold** button. My thoughts are fluid and stopping to find buttons and functionallity breaks my thought process and slows me down. There are a ton of really great Markdown editors. I use Sublime Text with the Markdown package for previewing what I'm doing.

### Github Pages make Jekyll even easier
As Bruce said in his article, when you push a jekyll structured project to your github pages repository, github will do the build for you. This allows anyone to use jekyll without having any of the tools on their own computer. I still prefer the tools so that I can view the site live ( `jekyll serve` ). Also, github makes a lot of sense since this is going to primarily be a techincal blog.

I'm not going to spend a lot of time migrating my old blog over. I'll simply archive it and make sure any visitors to that site know about this one. The articles are fairly dated at this point anyway. I do plan to keep this blog primarily technical although I suspect a few less technical posts might filter in now and again. I also maintain a tableop gaming web site and blog, Cardboard Carnage, which you can find a link to on the side bar under **Links**. I'll also be blogging for my startup, GenRocket, also linked to in the sidebar.

Here are the links I promised:

* [Jekyll docs][jekyll]
* [Jekyll and Github Pages][jekyll-gh]
* [Bruce Eckel: Using Github Pages for Blogging][bruce]

[jekyll]:		http://jekyllrb.com
[jekyll-gh]: 	http://www.smashingmagazine.com/2014/08/01/build-blog-jekyll-github-pages/
[bruce]:		https://bruceeckel.github.io/
[octopress]: 	http://octopress.org/
