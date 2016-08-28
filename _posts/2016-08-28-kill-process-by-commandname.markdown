---
layout: post
title: Kill process by command name
---

When you want to kill process, there are many ways you can do it. Most common is by calling `kill` command and use of PID. You can kill processes also by name or part of the name using `killall` or `pkill`. But name of process is not the same as command that starts it. Let's take for example [Spring], a Rails application preloader. You can’t kill it by typing `killall spring` because it’s a ruby process, and executing `killall ruby` will kill all ruby processes which is not always desired. Instead you can type:

{% highlight bash %}
kill `ps x | grep spring | grep -v grep | awk '{print $1}'`
{% endhighlight %}

This command lists all processes, selects one with spring name, fetches its ids, and kills these processes.

[Spring]: https://github.com/rails/spring "Spring, Rails application preloader"
