--- 
title: Using gmail for email on a heroku managed domain
date: 04/10/2010
layout: post
--- 

If you have a domain on [heroku](http://heroku.com) and wish to receive emails on
that domain then you need to configure your DNS to include MX records to tell
the "internet" where to send emails.

First of all you need to make sure you are using a custom domain for your heroku
website. By default you app will be

    myapp.heroku.com

but we want it to be 

    myapp.com

First you need to claim myapp.com as your own. Go to a domain registrar such
as GoDaddy or APlus and claim the name for yourself. It will cost you a few
dollars to do so.

Then in the heroku dashboard for your app, add two custom addons.

* Custom Domains
* Zerigo DNS

Custom domains is simple you just add the names you wish your website to
take

* myapp.com
* www.myapp.com

Zerigo DNS is a bit more complex. First you have to go back to your domain
registrar service and tell them that the DNS for myapp.com will be handled
by zerigo. You can do this by adding the zerigo name servers to your record.

* a.ns.zerigo.net	
* a.ns.zerigo.net	
* b.ns.zerigo.net	
* b.ns.zerigo.net	
* c.ns.zerigo.net	
* e.ns.zerigo.net	
* e.ns.zerigo.net	

Because Zerigo and Heroku are integrated all the settings are pre-configured
for you when you add the zerigo add on for you app.

Now you have done this you will want to set up email support for myapp.com.
Heroku does not have an add on to do this for you. Luckily you can easily
do this with gmail though you have to set up a new account to do this.

Goto [google apps signup](https://www.google.com/a/cpanel/domain/new) and register
for an account under myapp.com. Follow all the instructions for verifying that
you own the domain name. This will involve either modifying a DNS record or placing
some meta tags in your website.

You will notice on this website the meta tags

      <meta name="google-site-verification" content="E7Npt5MsLN18NcKvW7NaTLgVbeJKN7DhTscOnssFU9E"/>

this is how I verified my site to google. Obviously do not paste the above
into your website or I will be able to claim it as my own. 

Once your google apps account is setup you then need to setup some MX records with
Zerigo DNS to tell the internet to route your email to google.

Just add the following MX records to your account for myapp.com

* alt1.aspmx.l.google.com
* alt2.aspmx.l.google.com	
* aspmx.l.google.com	
* aspmx2.googlemail.com	
* aspmx3.googlemail.com
* a.gtld-servers.net
* a.gtld-servers.net

Now you are good to go. And now you have

* free app hosting with heroku
* free DNS with Zerigo
* free email with gmail


