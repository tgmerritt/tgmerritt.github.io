This is a really quick note - for preservation, for a reminder - about how you can start "losing" data with MongoDB.

Imagine that you have a Rails application, and one of your Index actions is to display in table-layout information about documents in a certain collection.

You might write a scope to return just the fields that you'd like to display, because the document contains far more fields than is necessary for the index.

{% highlight ruby %}
scope :jobs_table, ->(session) do
    where({
        :merchant_id => session[:admin_id],
        :start_date => {:$gte => Time.now - 1.month}
    }).fields(
        :job_number,
        :type,
        :job_type_name,
        :priority,
        :priority_name,
        :start_date,
        :end_date,
        :start_time,
        :end_time,
        :admin_id,
        :billed,
        :collected,
        :status,
        :customer_id
    )
    end
{% endhighlight %}

And you might want to massage these documents for some reason with an update action once they are returned, like a timestamp or whatever:


{% highlight ruby %}
def setup_job_type_name
    if self.job_type_name
        return self.job_type_name
    else
        jt = allJobTypes(@environment_id).find { |h| h['_id'] == self.type }
        type = jt.nil? ? "N/A" : jt.job_type
        self.update_attributes({:job_type_name => type})
        return type
    end
end
{% endhighlight %}

And you would find that this update action wipes out any other key:value pairs that exist in the document, because you used the .fields() operation to return a subset.

And this is very bad.

I haven't dug into MongoMapper about this yet - but I imagine that either someone should scream from the rooftops that you will only update a document with the fields selected in the where statement, or MongoMapper shouldn't assume that I want to overwrite fields which are not listed in the update operation, and instead of updating the Object in place, should find the Object using it's primary ID once again and only update what you pass.  

This was a terrible lesson in the school of hard knocks for me.