---
layout: post
title: ngrok - tunnel to localhost
---

Sometimes you need to share your web application with wider audience, but don't have any server where you can deploy you app - [ngrok] is there for you.

With ngrok you can expose a local web server to the internet. Basic usage is as simple as

{% highlight bash %}
$ ngrok 80
{% endhighlight %}

Ngrok starts and shows some info about current tunnel, including address on which your app is available. In our case it is `http://753eb05d.ngrok.com` pointing to `127.0.0.1:80`

{% highlight bash %}
ngrok

Tunnel Status                 online
Version                       1.7/1.7
Forwarding                    http://753eb05d.ngrok.com -> 127.0.0.1:80
Forwarding                    https://753eb05d.ngrok.com -> 127.0.0.1:80
Web Interface                 127.0.0.1:4040
# Conn                        24
Avg Conn Time                 7.89ms
{% endhighlight %}

Using various options you can customize tunnel.

### <a name="non_local"></a>Non-local app

Ngrok can forward traffic to another machine behind a NAT or firewall, just specify its address in your private network.

{% highlight bash %}
$ ngrok 192.168.0.2:80
{% endhighlight %}

### <a name="custom_subdomain"></a>Custom subdomain

On each start ngrok assigns random subdomain but if you wish you can open tunnel with custom subdomain using `-subdomain` option.

{% highlight bash %}
$ ngrok -subdomain=custom 80
{% endhighlight %}

### <a name="inspect_traffic"></a>Inspect traffic

Nice thing about ngrok is ability to check inbound requests. To do that you need to open [http://localhost:4040] in your browser. Ngrok captures all of the HTTP traffic so you can check both request and response in more details including headers. You can even replay a request.

![ngrok inbound requests]({{ site.url }}/assets/ngrok_inbound.png)

If you want to know more about ngrok usage, for example how to create config file for common use tunnels, see it's [usage page].


[ngrok]: https://ngrok.com/
[usage page]: https://ngrok.com/usage
[http://localhost:4040]: http://localhost:4040
