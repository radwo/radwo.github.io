---
layout: post
title: Bundler and Local Git Repos
---

Instead of switching local path of gem in **Gemfile**

{% highlight ruby %}
gem 'paperclip', path: "~/workspace/gems/paperclip"
{% endhighlight %}

with remote one

{% highlight ruby %}
gem 'paperclip', github: "thoughtbot/paperclip", branch: "master"
{% endhighlight %}

You can set local git repo via `bundle config`

{% highlight bash %}
bundle config local.paperclip ~/workspace/gems/paperclip
{% endhighlight %}

It saves lots of typing when developing or debugging gem locally. This feature can be used only when gem has a path to git repo, not when it's pulled from rubygems. You also need to set a branch otherwise you will receive an error:

> Cannot use local override for paperclip at /Users/dark/workspace/gems/paperclip because :branch is not specified in Gemfile. Specify a branch or use `bundle config --delete` to remove the local
override

Branch in Gemfile needs to stick with one used in local repo, otherwise `bundle` will exit with error:

> Local override for paperclip at /Users/radwo/workspace/gems/paperclip is using branch foo but Gemfile specifies master

Be aware that every commit to gem will update **Gemfile.lock** with new revision. So you need to ensure that your gem is updated remotly before pushing your app.

More info about this feature can be found in [documentation][Bundler], under "Local Git Repos".


[Bundler]: http://bundler.io/v1.6/man/bundle-config.1.html "Bundler Documentation"
