---
title: "Use Hugo With Github Pages"
date: 2021-05-03T14:00:19-04:00
aliases: [/post/use-hugo-with-github-pages]
---

By default [Github Pages](https://pages.github.com/) works automatically with [Jekyll](https://jekyllrb.com/) which is great! Sometimes though you want to try a different static site generator like say [Hugo](https://gohugo.io/); the good news is that with a little bit of jiggery pokery it's actually pretty easy to so!
 <!--more-->
For the most part, I followed [these instructions](https://gohugo.io/hosting-and-deployment/hosting-on-github/) to get setup. But there were some important things I needed to understand before it could work.

First of all you can in fact host a personal site (e.g. repo: `[username].github.io`) that publishes a different branch than `main`, this means you don't need two repositories as some guides suggest. 

You can basically use the Github Action from the instructions above almost verbatim. [That action](https://github.com/marketplace/actions/hugo-setup) will create a branch called `gh-pages` and commit the generated site to its root directory. This means that Github Pages will not try and run Jekyll because it will detect a static site at the `gh-pages` branch and just publish that.

For whatever reason, understanding how that all works took me a while and wasn't immediately obvious (to me) from the documentation.

Here is the workflow:

**Initial Setup**

* Create a repository on Github called `[yourusername].github.io`
* Push your newly created Hugo site to it as is
* Add the Github Action shown in [the instructions](https://gohugo.io/hosting-and-deployment/hosting-on-github/) to your repo
* Push that action (and make sure it runs)
* Go to your repo settings and select `gh-pages` branch for publishing, ignoring any Jekyll errors you see on the way
* Make a change to your site and push it once more
* Confirm that the site is published!

**Ongoing**
* Push changes as normal
* Confirm that the Github Action executed successfully
