---
layout: post
title:  "Nokogiri and XML"
date:   2014-01-18 12:13:43
categories: jekyll update
---

I was presented with the opportunity to integrate [Field Harmony]("http://www.fieldharmony.com") with [Service Bench]("https://www.servicebench.com/site/").  Service Bench is the hub for a lot of large manufacturers to dispatch warranty work orders to qualified servicers all around the US.  It's pretty important that Field Harmony supports their platform, so I set about integrating their API.

They use XML - so we use [Nokogiri]("http://nokogiri.org").

Because the actual code is heavily restricted under NDA - I'm going to write a completely fictitious example of constructing XML and communicating it over to a 3rd party.

There are probably two parts to any <em>secure</em> XML request-response pair:

1. Header with authentication information

2. Body with the actual request type and data

Let's start with constructing a header:
{% highlight ruby %}
def header(USERNAME,PASSWORD)
  Nokogiri::XML::Builder.new do |xml|
      xml.Envelope("xmlns:soapenv"=&gt;"http://schemas.xmlsoap.org/soap/envelope/", "xmlns:zzz"=&gt;"http://somesite/definition/stuff") {
        xml.parent.namespace = xml.parent.add_namespace_definition("soapenv", "http://www.w3.org/soap-envelope")
        xml['soapenv'].Header {
          xml['yyyy'].Security("soapenv:mustUnderstand"=&gt;"1", "xmlns:yyyy"=&gt;"http://security.hostname.com/dir/to/xsd.xsd" ) {
            xml.parent.add_namespace_definition("yyyy", "http://security.hostname.com/dir/to/xsd.xsd")
            xml['yyyy'].REDACTED("zzz:Id"=&gt;"REDACTED") {
              xml.parent.add_namespace_definition("zzz", "xmlns:zzz=http://security.hostname.com/dir/to/xsd.xsd")
              xml['yyyy'].Username USERNAME
              xml['yyyy'].Password("Type"=&gt;"http://some.site.com/path/to/something").text(PASSWORD)
            }
          }
        }
      xml['soapenv'].Body {
      }
    }
  end
end
{% endhighlight %}

(I'm using a wordpress-hosted blog at the moment, so I can't install code-snippets - apologies)

Now we have a header function we can use at-will for all future requests.

Let's make a body request:

{% highlight ruby %}
def req(name,from,to,node)
  Nokogiri::XML::Builder.with(node) do |xml|
    xml['zzz'].Request {
      xml['zzz'].Name (name)
      xml['zzz'].Ver ("1.0")
      xml['zzz'].From (from)
      xml['zzz'].To (to)
    }
  end
end
{% endhighlight %}

Cool - now we have a body as well.

But they aren't connected yet, are they?  We have two independent functions, one of which will work, the other will fail because we are passing in params that don't exist (assume USERNAME and PASSWORD exist in the header block).

You can see the 'node' element that we're passing into the 'req' block.  We have to make that thing.  And that's the beauty of Nokogiri.  It's easy.

Because we have the header and the body as separate functions to call, we need a third function from whence to call them!

{% highlight ruby %}
def make_request
  REDACTED PRIVACY STUFF
  name = Admin.find_by_id(session[:admin_id])
  dateF = Date.today - 7.days
  dateT = Date.today + 7.days
  from = dateF.strftime("%Y%m%d")
  to = dateT.strftime("%Y%m%d")
  builder = header(username,password)
  node = builder.doc.xpath('//soapenv:Envelope/soapenv:Body').first
  req(name,from,to,node)
  send_xml(builder.doc.root.to_xml)
end
{% endhighlight %}

Now we have a function which we can call from our front-end with a  button click via AJAX, or schedule it to run on a cron, or trigger however you want really, and it will create the header with the appropriate authentication information, stitch that to the request, and send it (the send function isn't explained in this post - come back for more!)

You could use this same pattern to represent any kind of request that requires the header.  You would just make a new function, call the header line (aka builder = ) and then pass it the node line because the "Body" is created blank inside the header (so it's always the first xpath that you want).

We're using this pattern and actually passing the send_xml immediately in order to get results.  The results go into a delayed_job queue and process in the background (we know the size of our request, we cannot always know how much information will come back!)