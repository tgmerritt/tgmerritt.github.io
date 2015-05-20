---
layout: post
title:  "Creating a new Rails4.2 app with PSQL and Devise"
date:   2015-05-20 16:58:43
categories: jekyll update
---

I hope that someone comes across this post someday and it's useful.  There are many tutorial blogs on how to get Rails setup and running, they're nicely laid out in sections with formatting, images of the console, and thorough explanation.  They are very useful.

This isn't one of them.

This is a brain-dump of notes on how I setup an application last night off the cuff with Rails 4.2 and PostgreSQL.

First thing I did was create a new gemset:

{% highlight ruby %}
rvm gemset use rails42
{% endhighlight %}

If you don't have rvm installed - you can find instructions here: <a title="Install RVM" href="https://rvm.io/rvm/install" target="_blank">Install RVM</a>

A gemset creates a kind of fenced off little world within which you can declare various gems you'd like to use.  It exists to help you create buckets such that you can test the same application on different versions of Ruby with it's dependent gems updated to the proper versions.

Then I declared I wanted to use Ruby 2.1.5 with this gemset:

{% highlight ruby %}
rvm use 2.1.5@rails42
{% endhighlight %}

If you want to see all the gemsets for a particular version of Ruby (each ruby can have it's own gemsets!) you can run this command:

{% highlight ruby %}
T$ rvm gemset list

gemsets for ruby-2.1.5 (found in /Users/Tyler/.rvm/gems/ruby-2.1.5)
   (default)
   global
=&gt; rails42

{% endhighlight %}

I didn't have PostgreSQL installed on the laptop I was using - so you do:

{% highlight ruby %}
brew install postgresql
{% endhighlight %}

I've seen people reference `brew install pg` and `brew install postgre` on some blogs - but this is ambiguous:

{% highlight ruby %}
brew install pg
Error: No available formula for pg 
Searching formulae...
apg           gpgme          mpg123         pgbadger        pgpool-ii           topgit
apgdiff           libbpg          mpg321         pgbouncer        pgrep           virtualpg
gnupg           libgpg-error          mpgtx         pgcli            pgrouting
gnupg-pkcs11-scd   libjpg          osm2pgrouting     pgdbf            pgtap
gnupg2           libpgm          osm2pgsql         pgformatter        pgtune
gpg-agent       libwpg          pg_top         pgpdump        rpg
Searching taps...
homebrew/versions/gnupg21          homebrew/php/phppgadmin            Caskroom/cask/pgadmin3
homebrew/php/php54-pdo-pgsql          homebrew/x11/pgplot            Caskroom/cask/pgloader
homebrew/php/php55-pdo-pgsql          Caskroom/cask/gpgtools            Caskroom/cask/pgweb
homebrew/php/php56-pdo-pgsql          Caskroom/cask/pg-commander
{% endhighlight %}

So don't do that.  Just spell it out...

Then you're ready to start a new app.  Go to whatever home folder you start all your applications from (mine is /Users/Tyler) and you can initialize the application like this:

{% highlight ruby %}
rails new myapp --database=postgresql --no-ri --no-rdoc
{% endhighlight %}

This is going to setup the application without all the unnecessary documentation files (which you can find online) and postgresql.

At this point - instead of copying and pasting parts of the application - if you want to see the sourcecode it's on GitHub at <a href="https://github.com/tgmerritt/loan_co_example" target="_blank">https://github.com/tgmerritt/loan_co_example</a>

The important parts are that you're going to get the 'pg' gem in your Gemfile right away.  That takes care of the adapter you need to talk to PostgreSQL.

Here is a part of the brain dump that I want to mention:

If you're going to use Devise for authentication, ALWAYS create the User model first.  I think their documentation and stuff I've seen on the web leads people to believe that if you run :

{% highlight ruby %}
rails g devise User
{% endhighlight %}

That it's going to create the User model for you and associated migrations.  But it doesn't.

So your order of operations should be:

{% highlight ruby %}
rails g scaffold User first_name:string last_name:string 
rails g devise:install
rails g devise User
{% endhighlight %}

Do NOT declare the email attribute or password on your User model - that's some of the stuff that devise does for you.  And if you declare it, you're going to get a problem when you run your migration because it'll tell you email already exists:

{% highlight ruby %}
== 20150210225259 AddDeviseToUsers: migrating =================================
-- change_table(:users)
rake aborted!
StandardError: An error has occurred, this and all later migrations canceled:

PG::DuplicateColumn: ERROR:  column "email" of relation "users" already exists
{% endhighlight %}

See?  Learned the hard way.

The other thing that I didn't understand was how the migration didn't create the foreign keys for me automatically.

If you use stuff like:

{% highlight ruby %}
class User &lt; ActiveRecord::Base
  has_many :posts
  has_one  :avatar
end
{% endhighlight %}

Or whatever your relationships are, you expect the Post table to have a user_id column that magically performs the association for you such that you can call:

{% highlight ruby %}
user = User.find(params[:id])
user.posts.first
{% endhighlight %}

Or something like that.

I can't recall actually creating this migration with MySQL, but maybe I did a long time ago in some apps.  With MongoDB you don't have to create the migration (because there aren't migrations) so I guess I'm just fuzzy on this particular act of manually creating the foreign keys.

I had to write a subsequent migration that changed each table and added the key columns that I wanted.

{% highlight ruby %}
class AddFeeIdToLoan &lt; ActiveRecord::Migration
  def change
    add_column :loans, :fee_id, :integer
    add_column :loans, :user_id, :integer
    add_index  :loans, :user_id
    add_index  :loans, :fee_id
  end
end
{% endhighlight %}

Then you can rake db:migrate to your heart's content and it will all be setup for you.

When you start your application you're going to be told some stuff isn't right.  Follow the warning messages and fix the problems (not going to document each one).

Mostly they have to do with config options either missing from one of the environment files, or stuff is deprecated and you need to change the wording.

One big thing when I went to deploy on Heroku was that WEBrick wouldn't start (I was just messing around with some example stuff - didn't need a real web server, Procfile, etc.)

Turns out that despite it being a BRAND NEW Rails app - the bin directory was missing.  Do this:

{% highlight ruby %}
rake rails:update:bin
{% endhighlight %}

That's going to create some pretty important stuff that Heroku needs.  It would be nice if Heroku could tell you about this - I had to Google the answer and fix it that way.

By this point you should be developing.  You should be able to create your Heroku app, push, and it should work, with the free PostgreSQL addon that Heroku provides backing up the data!
