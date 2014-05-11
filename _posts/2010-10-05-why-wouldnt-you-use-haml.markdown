--- 
title: Why wouldn't you use haml?
date: 05/10/2010
layout: post
--- 

The template for the layout for this blog

    <!doctype html>
    %html
      %head
        %link{:href => "/stylesheets/compiled/blog.css", :rel => "stylesheet", :type => "text/css"}
          %link{:href => "/index.xml", :rel => "alternate", :title => "#{title} - feed", :type => "application/atom+xml"}
          %meta{:content => "text/html; charset=UTF-8", "http-equiv" => "Content-Type"}/
          %meta(name="google-site-verification" content="E7Npt5MsLN18NcKvW7NaTLgVbeJKN7DhTscOnssFU9E")
          %link{:href => "/js/highlight/styles/zenburn.css", :rel => "stylesheet", :type => "text/css"}
          %link{:href => "http://www.google.com/profiles/bradphelan", :rel => "me", :type => "text/html"}
          %script{:src => "/js/highlight/highlight.pack.js", :type => "text/javascript"}
          :javascript
            hljs.initHighlightingOnLoad();
          %title= title
      %body
        .two-col
          #header
            #logo
              %h1 
                %a{:href => "/"}
                  %img#wave{:src => "/img/wave.jpg"}/
                  %span Brad Gone Surfing
          #content
            %div
              %section
                :preserve
                  #{yield}
          #sidebar
            #links
              %h5 Contact Me 
              :markdown
                * [email](http://www.google.com/recaptcha/mailhide/d?k=01iuumTes5m7GytcPhCI5CCg==&c=VAWZLvR6KyAQVZOfQmhbpbXJgmz5uolVTdy5DahvMts=)
                * [github](http://github.org/bradphelan)
                * [linkedin](http://at.linkedin.com/in/bradphelan)
                * [xing](https://www.xing.com/profile/Brad_Phelan")
                * [twitter](http://twitter.com/bradgonesurfing)

              %h5 Links
              :markdown
                * [wmii](http://wmii.suckless.org) 

              %h5 
          #footer
            :markdown
              powered by
              [toto](http://cloudhead.io/toto)

Note the mixing and matching of pure haml for structural layout and markdown for content areas. The markdown
generated lists are easily styled with a lump of Compass / SASS to have no bullets and have no indent.

    @import "compass/utilities/lists/bullets"

    #links > ul
      +no-bullets
      padding: 0px
      a
        text-decoration: none


