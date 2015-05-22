---
layout: post
title:  "Watch your ENV vars"
date:   2014-01-16 10:37:43
categories: jekyll update
---

Quite a few times while I have been working on [Field Harmony]("http://www.fieldharmony.com"), I ran into the error depicted below:

{% highlight ruby %}
[Worker(host:melchior.local pid:9863)] Starting job worker
rake aborted!
undefined method `collection' for nil:NilClass
{% endhighlight %}

It is the undefined method `collection' part that really confuses me each time I see this.  I always think that there must be some problem with my database, such as a collection having been dropped.  Naturally, the error message isn't really indicative of the underlying problem.  The problem as it turns out is I use environment variables to avoid checking in my username and password to version control (aka I don't want GIT to have the keys to the kingdom).

When I finally have my eureka moment and fix the issue, I shake my head that I wasted so much time remembering that I've stubbed my toe on this a dozen times before.

If you have a Rails application using a YAML file to load configs for any reason (I use it for a few things, the mongo connection being one), and your YAML contains something to this effect:

{% highlight ruby %}
production:
  database: <%=ENV["MONGO_DATABASE"]%>
  host:     <%=ENV["MONGO_HOST"]%>
  port:     <%=ENV["MONGO_PORT"]%>
  username: <%=ENV["MONGO_USERNAME"]%>
  password: <%=ENV["MONGO_PASSWORD"]%>
{% endhighlight %}

Then you need to make sure that you've exported those values into the appropriate place for your development.  I use a Mac, so I have to export them into whatever Terminal window I'm using to run Unicorn or the rake task.  I don't save them into any profile and try to always wipe my history to keep things as secure as possible (I'm not perfect at this practice!).  Which means in my case, whenever I open a new Terminal window and I'm going to use it for some Rails-server related thing, I need to export these values.

Lesson learned - don't let the error message itself confuse you.  If you know that there isn't anything wrong with your <em>collections</em>, assume there is a problem with the <em>connections</em>.