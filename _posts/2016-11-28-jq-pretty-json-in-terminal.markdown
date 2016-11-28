---
layout: post
title: jq - pretty json in terminal
---

JSON is a standard all modern REST APIs use. It’s very human friendly, but only when properly formatted. Unfortunately not every server returns it nicely. And when it becomes a little more complicated, reading it in terminal will be difficult.

Help comes to us from [jq] - lightweight and flexible command-line JSON processor. Here is a sample json prettified by it:
{% highlight bash %}
$ echo '[{"foo": {"bar": "baz"}}, {"foo": "baz"}]' | jq .
[
  {
    "foo": {
      "bar": "baz"
    }
  },
  {
    "foo": "baz"
  }
]
{% endhighlight %}

Besides pretty-printing jq is really powerfull tool to process JSON. We can, for example, use it to extract just the first item, and access its nested attributes.

{% highlight bash %}
$ echo '[{"foo": {"bar": "baz"}}, {"foo": "baz"}]' | jq '.[0] .foo'
{
  "bar": "baz"
}
{% endhighlight %}

More examples can be found in [tutorial] and [manual], enjoy!

[jq]: https://stedolan.github.io/jq/
[tutorial]: https://stedolan.github.io/jq/tutorial/
[manual]: https://stedolan.github.io/jq/manual/
