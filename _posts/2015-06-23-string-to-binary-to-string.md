---
layout: post
title:  "String to Binary to String"
date:   2015-06-23 12:14:57
categories: jekyll update
---

The most ridiculous thing
=========================

Here's the problem, we just upgraded all of our internal applications (15!) to Ruby 2.2.2 from Ruby 2.0.0-p645.

Aaaaaaaaaaaaand something changed of course.  Something in the way that strings are encoded.  

We use wkhtmltopdf and pdftk in order to create PDFs and fill them out with customer information (pre-filling in the form with relevant info to save our users some time, things like their name in the name field, etc.)

Well if you've ever worked with PDFs and Ruby before, you know that this is a giant pain in the ass.  When you have HTML that you want to convert to PDF, you're basically taking the entirety of the HTML, converting it to one massive string, and then rendering the PDF from this string.  There are other ways to do it of course, but for the sake of this post let's just agree that's what is happening here under the covers.

So in Ruby 2.0.0-p645 we can force encoding globally, and it was forced to something like ASCII_8BIT.  Upgrading to Ruby 2.2.2, all of our PDFs started rendering blank.  No error message, no tip off that there was corruption of any kind anywhere.  Silent failures are the things I see in my nightmares.

So I start digging around in the code and come upon an old comment:

{% highlight ruby %}
# FIXME: hack to convert ActionView::OutputBuffer to String to make HTTP client process it correctly.
{% endhighlight %}

Cool.  Thanks.  You put in a hack back in 2013, left the company, didn't tell anyone about it, and BOOM - time bomb for the next dev.  

Here is the hack:

{% highlight ruby %}
string_data = ('' + data).to_s
{% endhighlight %}

...

Adding that leading empty string before the data stream and then stringifying the entire thing apparently avoided an invalid sequence error for UTF-8 encoding (why everything wasn't in UTF-8 from the beginning is beyond me...  We aren't dealing with anything that shouldn't just be the most generic encoding available).

I'm going to cut straight to the end and show you what we had to do in order to get these PDFs to render properly once again.

From the service that sends the encoded data to the system that stores that data on disk as a PDF:

{% highlight ruby %}
string_data = ('' + data).to_s.unpack('C*')
response = connection.post(documents_url, params.merge(:file => string_data))
{% endhighlight %}

Here we have to call unpack on the stringified data to convert it to binary.  There is a [great blog post](http://blog.bigbinary.com/2011/07/20/ruby-pack-unpack.html) on using pack and unpack that you can read for the dirty details.

When we get to the other service that receives the binary stream, we had to write:

{% highlight ruby %}
content = content.respond_to?(:pack) ? content.map(&:to_i).pack('C*') : content.to_s
{% endhighlight %}

It's very likely that content will always need to be packed from now on.  However, just in case anyone is using the old way of "writing" to this service (this code exists inside the 'write' method as a generic endpoint for other services) we're going to preserve the old way of doing things (which was much simpler if you ask me).

