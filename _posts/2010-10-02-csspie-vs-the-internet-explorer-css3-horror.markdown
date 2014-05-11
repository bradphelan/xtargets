--- 
title: CSSPIE vs the Internet Explorer CSS3 Horror
date: 02/10/2010
layout: post
--- 

I mentioned in my last post the horror that was internet explorer CSS3 support. I just
came across [CSSPie](http://css3pie.com/) which takes advantage of an obscure IE only
CSS feature called behaviours to hook in a script which renders the missing CSS3 support
using IE VML.

In theorey it should be as simple as modifying my border mixin

    =standard_border
      +box-shadow(darken($background, 30%), -5px, 5px, 15px)
      +border-radius(15px, 15px)

to

    =standard_border
      +box-shadow(darken($background, 30%), -5px, 5px, 15px)
      +border-radius(15px, 15px)
      behavior: url(/PIE.htc);

and including PIE.htc in my static route. 

I integrated this into my SASS file uploaded to Heroku and then check the 
layout with an [online ie render checker](http://ipinfo.info/netrenderer/index.php)
and it all worked brilliantly.

Now my blog renders nicely in all modern browsers. 

Thankyou very much [CSSPie](http://css3pie.com/)



