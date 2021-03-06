---
layout: post
title:  "Hello World"
date:   2014-01-15 14:36:04
categories: jekyll update
---

Hi, I'm Tyler, and I blog here about software.

My business partner and I have been working on <a href="http://www.fieldharmony.com">Field Harmony</a> for the past 9 months or so.  It's a service-industry focused hosted application written in Ruby on Rails with MongoDB.

Recently I had a need to integrate backgrounding of jobs for certain aspects of the application.  The <a href="https://github.com/collectiveidea/delayed_job">delayed_job gem</a> was just the ticket.  But we wanted to save cost on running Heroku dynos, so we paired this gem with the simple but excellent <a href="https://github.com/lostboy/workless">workless gem</a>.  In the first test, the staging environment failed catastrophically.

We received a message about Heroku being unable to complete the request (which was to spin up the worker-dyno as a backgrounded job had been added to the corresponding collection).

The application trace showed an issue with the post_ps_scale command from the Heroku API gem.  Oh great - some out-of-date code tested against the wrong stack (I thought).

There are <a href="https://www.google.com/#q=post_ps_scale">plenty of posts</a> on stack overflow pointing to this same method as the culprit for various problems.  I was about to log a new issue against the Heroku API gem when we stumbled upon a comment for a particular SO post (which I can't find again as of the writing of this blog...) that spelled out the problem beautifully:

"Make sure you have a valid credit card on file with Heroku - or this won't work.  The post_ps_scale command is responsible for automatically scaling the dynos (which costs money) and without a valid card on file, Heroku will reject this API call."

BINGO.

Many of us in the dev world, working on new businesses or projects, use Heroku to deploy into early production because it's FREE to do so for a small application with limited users.  Heck, I'll bet there are many profitable (but small) applications out there which have never paid Heroku (or Amazon for that matter) a dime.

In hindsight it's obvious that Heroku would probably have the parts of their system which might generate money for them polished very, very well, but it's odd that the Heroku gem wouldn't do a better job of simply returning "no card on file" as part of the error message.  Heroku authored the thing, you'd think they would save us a lot of time by just telling us to pay up!

&nbsp;