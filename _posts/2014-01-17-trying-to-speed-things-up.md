---
layout: post
title:  "Trying to speed things up"
date:   2014-01-18 12:13:43
categories: jekyll update
---

I've seen people post about the built-in Benchmark feature of Rails, but I'd never bothered using it until yesterday.

I had a problem (seems like everything is a problem ^_^) - our reporting page was loading really slowly.  We put it together quite early in the development of our [Field Harmony]("http://www.fieldharmony.com") knowing that we would need data before we could circle back to it and tweak everything to make it a powerful, useful feature.

The reports page on my local environment was taking about 32 seconds to load completely for a particular customer (with a lot of data).  So I set out to find ways to speed things up.

I'll only share a single instance here because I want to demonstrate Benchmark more than talk about every aspect of our reporting system.

Before you read any further, there is an excellent guide on using Benchmark in Rails at [RubyLearning.com]("http://rubylearning.com/blog/2013/06/19/how-do-i-benchmark-ruby-code/")

Let's take this block of code:
{% highlight ruby %}
 puts Benchmark.measure{ @revenue = Job.where(:merchant_id => session[:admin_id].to_s, :addProdTime.ne => nil).fields(:addProdTime, :products, :billed).sort(:addProdTime).to_a }
 puts Benchmark.measure{ @test_revenue = Job.where(:merchant_id => session[:admin_id].to_s, :addProdTime => {"$ne" => nil}).fields(:addProdTime, :products, :billed).sort(:addProdTime).to_a }
{% endhighlight %}

Here we have two lines yielding the same result, and I wanted to see if the :key.ne => nil syntax was faster than :key => {"$ne" => nil} syntax.

I could have written it like :

{% highlight ruby %}
Benchmark.bm do |bm|
  bm.report do
    iterations.times do
      @revenue = Job.where(:merchant_id => session[:admin_id].to_s, 
      :addProdTime.ne => nil).fields(:addProdTime, :products, :billed).sort(:addProdTime).to_a
    end
  end
  bm.report do
    iterations.times do
      @revenue = Job.where(:merchant_id => session[:admin_id].to_s, 
      :addProdTime => {"$ne" => nil).fields(:addProdTime, :products, :billed).sort(:addProdTime).to_a
    end
  end
end
 {% endhighlight %}
But that seemed like a lot of trouble (which I took for the blog so I'll go back and paste it in to actually run it ^_^).  So I took the easy way out.

The result from that one test one time looked like this:

Test 1  0.470000   0.120000   0.590000 (  0.621368)
Test 2  0.440000   0.130000   0.570000 (  0.591526)

What's interesting from the single test is that it seemed to take the system slightly longer to process the request in one way versus the other (my code), but took less time to actually process the result (CPU time).

One measly test doesn't tell us anything of value.  Since I'm going to paste it into the code and run it anyway I'm going to pause here and come back with better data.

aaaaaand we're back.

I ran the test 20 times each, you could do something 10 million times, but because I'm not dropping to the Ruby console level (I'm still processing these things through my web server locally - it's a web-app, and web-apps have timeouts and other considerations that I can't just ignore), I kept the sample size small.

Here are the results:
{% highlight ruby %}
user     system      total        real
8.290000   2.510000  10.800000 ( 11.109513)
8.360000   2.460000  10.820000 ( 11.127391)
{% endhighlight %}

As you can see, there is really no appreciable difference between the two syntaxes.  I would appear that :key.ne => foo syntax is just every so slightly faster which can be attributed to the fact that there are simply fewer characters to parse.  All PERL-programmers will agree, less is more.