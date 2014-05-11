--- 
title: Caching signed uris with Paperclip and Amazon S3
date: 08/03/2012
--- 

With paperclip we can specify that a generated URL will
expire in a certain period of time. The simple way to do
this is just

    user.avatar.expiring_url(24.hours, :thumb)

The bad thing about the above is that it generates a new
url on every call and every time the user refreshes the
page they get new image urls. The browser doesn't get 
the chance to cache the page and your S3 bandwidth bill
goes up.

The better way is to use an explicit date for the expiry.

If you would like your images to expire after a **maximum**
of 25 hours then create a method like below in your application
helpers file

    def s3_expiry
          Time.zone.now.beginning_of_day.since 25.hours
    end

Then you can do

    user.avatar.expiring_url(s3_expiry, :thumb)

The image url will expire at 1am in the morning tomorrow nomatter
how many time time you regenerate the url. This means the url
stays stable and is suitable for browser caching for
25 hours. However at a maximum of 24 hours new URL's are generated
leaving a minimum cache window of 1 hour and and average of 12.5
hours of caching.

