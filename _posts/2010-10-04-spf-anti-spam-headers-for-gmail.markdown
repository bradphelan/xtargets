--- 
title: SPF anti SPAM headers for GMAIL
date: 04/10/2010
layout: post
--- 

My previous post showed how to set up gmail for your own custom domain. A friend
pointed out that any email I send through gmail for *xtargets* may be considered spam. 
This is because some mail gateways are implementing authentication techniques to detect
if an email is ham or spam.

One way to authenticate an email is to declare via a DNS record which machines are
allowed to send email on behalf of xtargets.com. The way we do this is add an
SPF record to our DNS for *xtargets.com*. As before I do this with the Zerigo
web gui associated with my heroku account.

I add a TXT record and populate the data section with

    v=spf1 include:_spf.google.com ~all

If we do an 

    nslookup -type=TXT _spf.google.com

we get

    _spf.google.com	text = "v=spf1 ip4:216.239.32.0/19 \
                            ip4:64.233.160.0/19 \
                            ip4:66.249.80.0/20 \
                            ip4:72.14.192.0/18 \
                            ip4:209.85.128.0/17 \
                            ip4:66.102.0.0/20 \
                            ip4:74.125.0.0/16 \
                            ip4:64.18.0.0/20 \
                            ip4:207.126.144.0/20 \
                            ip4:173.194.0.0/16 \
                            ?all"

which are the IP address ranges that google delivers mail from. 

I can prove this to myself by sending an email to another account
I have on yahoo and inspecting the headers I find that yahoo
has checked the SPF record and verified the authenticity of
my email.

    Received-SPF: pass (mta1000.mail.sp2.yahoo.com: \
                             domain of bradphelan@xtargets.com \
                             designates 209.85.214.173 as permitted sender
