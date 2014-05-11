--- 
title: Testing OAuth 2.0 apps locally
date: 05/03/2012
--- 

In your facebook app setup do

    App Domain => mydomain.com

in your /etc/hosts

    127.0.0.1 localhost.mydomain.com
    ::= localhost.mydomain.com

in a shell script

    #!/bin/sh
    sudo ssh -L 80:localhost:3000 -l `whoami` -N localhost

When this script is run then it will block. Press ^C to stop
the port forwarding. This could be made into a rake task if 
you like

What the above does is tell facebook to authorize an application operating at
the *.mydomain.com uri pattern. In our /etc/hosts we map localhost.mydomain.com
to the loopback device and in the shell script we forward port 80 to the real
port our rails app runs on. Last of all we need to configure OAuth in our
OmniAuth in our rails app to use the correct end points depending on our
environment.


    if Rails.env.production?
      OmniAuth.config.full_host = "http://mydomain.com"
    elsif Rails.env.test?
      OmniAuth.config.full_host = "http://staging.mydomain.com"
    elsif Rails.env.development?
      OmniAuth.config.full_host = "http://localhost.mydomain.com"
    end


If you are using http rather than https then change the http to http above.
