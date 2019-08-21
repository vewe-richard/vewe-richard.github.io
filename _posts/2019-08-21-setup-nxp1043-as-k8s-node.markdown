---
layout: post
title:  "Setup NXP LS1043A as a Kubernetes Node"
date:   2019-08-07 17:44:17 +0800
categories: jekyll update
---

This post tell how to setup NXP LS1043A as a Kubernetes node, and how to fix some issues during the process.

# Overview

This setup need two PCs or VMs, one as Kubernetes master, one as node. Also, one NXP LS1043 box is needed to be another node.

![overview1](https://raw.githubusercontent.com/vewe-richard/vewe-richard.github.io/master/_posts/images/nxp1043ask8snode-model.png)

And this setup will deploy a kubernetes service associated with two kubernetes pods, each has one container. Like below.

![architecture](https://raw.githubusercontent.com/vewe-richard/vewe-richard.github.io/master/_posts/images/nxp1043ask8snode-architecture.png)

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
