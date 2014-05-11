--- 
title: Understanding and solving race conditions with ruby rails and background workers
date: 08/03/2012
layout: post
--- 

Once upon a time you wrote a web application where a request would come in, you
would process the request and send the result to the client. Job done. These days
it is a little more complex. The jobs that a request may invoke may take longer
than the client is willing to wait for a browser response so we add jobs to a
job queue.


Sending an email is the cannonical example. A client performs some action in
the browser which invokes a response from the server. Part of that response
may be to send an email. However if we wait till the email is sent before
we respond to the browser it may take a long time and result in a grumpy
client.

Background workers to the rescue! Almost.

~

Now if your background workers feed from a job queue in the same database as
your models, [Delayed Job](https://github.com/tobi/delayed_job) being an
example of this, then you have not much to worry about and you can stop
reading.

However if you are using [Girl Friday](https://github.com/mperham/girl_friday)
which is memory/actor based or or [Resque](https://github.com/defunkt/resque)
which is Redis based or any other queuing mechanism which is not based on
ActiveRecord and resident in the same db as your models you are in for a scary
ride of race conditions unless you are very very careful.

Picture the following controller

    class FooController < ActionController::Base

      respond_to :html

      def create
        
        @foo = Foo.new params[:foo]
        @foo.save

        mail_subscribers_with @foo.id

        respond_with @foo

      end

    end

Our imaginary mail_subscribers_with method might use Girl Friday as it's
job mechanism so we define it like

    def mail_subscribers_with id
       
       MAIL_SUBSCRIBERS_QUEUE.push :id => id

    end

and the queue is defined in config/initializers/girl_friday.rb


    MAIL_SUBSCRIBERS_QUEUE = GirlFriday::WorkQueue.new :mail_subscribers_queue, :size => 3 do |info|
      
      foo = Foo.find info[:id]

      # A custom ActionMailer class to send the email
      SubscriberMailer.mail(foo).deliver

    end


The problem with this is that the queue can be too quick. Each controller
action is wrapped in a transaction in rails. Even though it **seems** that we
have saved the object to the db before we add the job to the queue this is
misleading. Transactions are local to the current thread in Rails. On another
thread in another transaction the new object will not appear until the first
transaction is commited. 

The end effect is that

    Foo.find info[:id]

may turn up no record or it may turn up a record. Not a good thing. Race 
conditions suck.

[After Transaction Gem](https://github.com/grosser/ar_after_transaction) to
the rescue.

This gem allows you to delay a block of code until after the current
transaction is complete and thus everything that will be is commited
to the database. To use it we modify our **mail_subscribers_queue**
method to be

    def mail_subscribers_with id
       ActiveRecord::Base.after_transaction do
         MAIL_SUBSCRIBERS_QUEUE.push :id => id
       end
    end


Now by the time the 

    Foo.find info[:id]

call is made in the work the object is garunteed to exist. The astute
reader will note the Rails 3 **after_commit** performs a similar function
but after_transaction is more flexible and can be used inline
with other source code making the intention of the code more obvious.


  
