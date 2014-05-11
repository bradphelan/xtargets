--- 
title: Ruby net-ssh with sudo
date: 12/10/2010
layout: post
--- 

Monkey patch Net/SSH as below

    #!/bin/ruby
    require 'net/ssh'

    class Net::SSH::Connection::Session
        def sudo password, command
            exec %Q%echo "#{password}" | sudo -S #{command}% 
        end
    end

then you can do

    Net::SSH.start("hostname.com", "username", :password => "password") do |ssh|
      ssh.sudo "password", "touch /tmp/foo_sudo.txt"
    end



