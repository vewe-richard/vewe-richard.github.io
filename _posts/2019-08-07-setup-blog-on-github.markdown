---
layout: post
title:  "Setup Blog on Github"
categories: jekyll update
---

Quick start
===== 
1. Follow [Quick Start][quick-start] of Jekyll to create a blog site.
1. Commit the blog site to github

   Github reserve repository name **username.github.io** for user to publish a static website.

   Follow below steps to publish your blog on github:

  * Create a new repository with name as **username.github.io**, for example my website repository is [vewe-richard.github.io][vewe-richard-repository].
  * Add all files of your site to this repository.
  * Preview your blog --- `http://username.github.io`

[quick-start]: https://jekyllrb.com/docs/
[vewe-richard-repository]:https://github.com/vewe-richard/vewe-richard.github.io

Maintain your blog
===== 
to maintain your blog, you need learning more about Jekyll, markdown

  * [Tutorial Jekyll](https://jekyllrb.com/docs/step-by-step/01-setup/)
  * [Markdown Quick Reference](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

maintain steps
  * Install Jekyll, for example, [install Jekyll on Ubuntu](https://jekyllrb.com/docs/installation/ubuntu/)
  * run `bundle install` to install missing gems.
  * Clone your website from github
  * Under your website directory, run `bundle exec jekyll serve`
  * preview the blog on local host `http://localhost:4000`
  * run `bundle exec jekyll build` when you change any template

some tips
  * jekyll only generate html for markdown file under the first level directory, for example /inner, /images. check this under temporary directory ./_site/


Reference
===== 
  * [Creating and Hosting a Personal Site on GitHub](http://jmcglone.com/guides/github-pages/)



