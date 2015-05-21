---
layout: post
title:  "A follow on to: Why I won't do your coding test"
date:   2015-05-21 13:38:04
categories: jekyll update
---

I have a habit of perusing Reddit pretty much non-stop, all hours of the day or night.  I like to frequent development-related subs, and there was a particular blog linked the other day that had something which struck a chord in me.

The original post about [Why I won't do your coding test](http://www.developingandstuff.com/2015/05/why-i-dont-do-coding-tests.html) addresses something that I've personally been bitten by in the past.

I'd like to present a couple of my responses in the discussion partly because I want to preserve it for myself, partly because I just recently started using Jekyll and I've got that fresh enthusiasm for blogging.

One of the commenters was in a hiring position, and basically disagreed with the original post.  His comment was along the lines of "I don't know if the code you're showing me from GitHub is really yours, so I have to test you."

I replied:

>If I write FizzBuzz like this do you give me the job or tell me my code is "too complex" and "not self-documenting" ?

{% highlight ruby %}
puts (1..100).map { |i| (fb = [["Fizz"][i % 3], ["Buzz"][i % 5]].compact.join).empty? ? i : fb }
{% endhighlight %}

And he replied that he could see my code was correct, but wouldn't hire me for a Senior role unless I could prove that this isn't the type of code I would write in production.

The reply that I'd like to preserve, because other commenters encouraged the rightness of it and upvoted accordingly, is:

Right - the point I'm making is that if you have a 5 minute test, I'm going to give you the 5 minute answer. And like OP's article, I don't believe that tests for interviews are valuable at all. I believe that in-person pairing for multiple hours, while a hardship on the candidate, is far more revealing of their skills as a problem-solver.

I'm not a great programmer. I'm a decent programmer who tries to maintain excellent habits. I'm not a math genius. I don't have algorithms and complex and obscure patterns memorized. I frequently refer to the documentation for even trivial things because I can never remember just how many arguments a particular method takes, if order is a concern, etc.

But the code I write I drive through tests. I re-factor mercilessly, I hate everything I write and look for any way to delete things as soon as I find a more concise, yet still understandable, way to achieve the objective.

None of that is going to come across in a 5-minute coding challenge.

Ditch the tests - you might have more candidates than you think. Plus, every business on the planet has "their way" of doing things. Your best practices and patterns are challenged daily in new blogs and "we found a better way to do X" posts all across the Internet. There is always some guy who thinks that defining custom routes is totally ok, and some other guy who adamantly insists that RESTful verbs are the only things allowed as endpoints to any API.

You know what the truth is? The truth is that the team of developers who work on the code base need to all agree on and implement the conventions the same way. At the end of the day, it doesn't matter if you only use create, new, update, edit, etc. or you custom-define every route you want. If 100 people are rowing a ship and they're all pulling on the oars at the same pace, with the same effort, you're going to have a good time.

And this has to be TAUGHT. Because the pace and direction at EVERY company is unique.

So stop filtering out people based on some stupid test that you think is correct. You've likely disqualified many applicants who, with a little bit of positive training and instruction, could adapt to what you want easily and fit your system. But if your test filters out people, like me, because the code you got wasn't "what you were looking for" - perhaps you should change your point of view.

For the record - the FizzBuzz that I keep in my back pocket as a party trick is the one you saw above. The FizzBuzz I would write in production would have a fizz_buzz_spec.rb to drive each step of writing the actual class. But that takes maybe 20 - 30 minutes to reproduce from scratch, despite muscle memory, and I don't know if the company deserves my 30 minutes in exchange for their salary offer. My time is infinitely valuable to me. Any offer at ANY amount is not worth what I think my time is worth. So I settle for the largest amount I can get. Everyone should be out for the same. 1 hour of time with my daughter is more precious than a mountain of gold to me.




