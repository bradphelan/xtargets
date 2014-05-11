--- 
title: The new CSS is SASSY
date: 01/10/2010
--- 

By all admission I am a CSS luddite. Floating is for surfboards. Anyway I've taken a look at CSS
over the years and it made me feel green every time I looked at it. I'm a programmer by all account,
C, C++, Java, Ruby, Python and a few others picked up and dropped off along the way.

However CSS is not a programming language. Not at least it the modern sense. It encourages cut
and paste programming which whenever I come across it makes my head steam. When I mean cut and
paste programming I mean the tendency to replicate chunks of code everywhere with no mind to
abstraction or refactoring. 

In CSS you have two choices

Choice A:

Litter your html markup with presentational logic repeated everywhere. This is the recommended
approach but it is plain wrong from a abstraction point of view. You want to tag your document
structure with classes and id's that represent the *meaning* of the structure not the layout.

Choice B:

Litter your css markup with snippets of duplicated CSS. If you want semantic classes and id's
for your html markup and have thus rejected choice A then you are forced to cut
and paste large chunks of CSS code around into each of your semantic classes.

For example to get nice, cross browser,  rounded shaded borders on a div. A css
snippet would look like

of CSS

    #content {
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding: 1em;
      margin: 1em;
      background: white;
    }

and if you had another div that was to be styled the same then add

    #foo {
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding: 1em;
      margin: 1em;
      background: white;
    }

and so on and then when you want to change the style of all these snippets of code
then you get involved in a VIM regexp deathmatch with yourself trying to come up
with some heroic bit of editor macro that will change all the styles for you.

At the points somebody says but why don't you just define a style called 

    #pretty_border {
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding: 1em;
      margin: 1em;
      background: white;
    }

and then used that in the HTML. But that is choice A and we have rejected that for
good reason. 

Along comes SASS to the rescue. SASS is a CSS compiler http://sass-lang.com/ and takes all
the hardwork away. I designed the layout and style for this blog using SASS and a library
of SASS modules called Compass. My current sass stylesheet is

    @import "compass/utilities/tables/borders"
    @import "compass/css3"
    //@import "../../css/main.css"
    @import blueprint
    @import "blueprint/grid"
    @import "compass/utilities/lists/bullets"
    @import "compass/utilities/links/unstyled-link"

    $background : #222

    // This will be our standard border settings
    =standard_border
      +box-shadow(darken($background, 30%), -5px, 5px, 15px)
      +border-radius(15px, 15px)

    // ---------------------------
    // This will be our grid model
    // ---------------------------
    body 
      +blueprint-typography

      .two-col
        +container
        +standard_border
        padding: 1em
        background-color: #ccc

    #header
      +container
      #logo
        background-color: darken(#ccc,10%)
        +standard_border
        padding-left: 1em
        margin: 20px
        +column(18)

    #header, #footer
      +column(24)
    #content
      +column(19)
    #sidebar
      +column(5, true)
    // ---------------------------
    // End grid model
    // ---------------------------
      
    /* code fragments */
    pre
      code
        +standard_border
        font-family: Monaco, monospace
        background: black
        overflow: auto
        padding: 10px
        margin-left: 0em
        margin-right: -180px
        margin-bottom: 2em

    #links
      +standard_border
      padding: 1em
      margin: 1em
      background: white

    h1, h2, h3
      a
        text-decoration: none

    ul.links
      +no-bullets
      padding: 0px
      a
        text-decoration: none

    // We have a square image so we want to round
    // off the corners a little.
    #wave
      +border-radius(15px, 15px)
      +box-shadow(darken($background, 30%), 0, 0, 15px)
      border-width: 5px
      border-color: #1f1f1f

and it expands to

    /* line 39, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body body {
      line-height: 1.5;
      font-family: "Helvetica Neue", Arial, Helvetica, sans-serif;
      color: #333333;
      font-size: 75%;
    }
    /* line 68, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h1, body h2, body h3, body h4, body h5, body h6 {
      font-weight: normal;
      color: #222222;
    }
    /* line 69, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h1 img, body h2 img, body h3 img, body h4 img, body h5 img, body h6 img {
      margin: 0;
    }
    /* line 70, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h1 {
      font-size: 3em;
      line-height: 1;
      margin-bottom: 0.50em;
    }
    /* line 71, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h2 {
      font-size: 2em;
      margin-bottom: 0.75em;
    }
    /* line 72, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h3 {
      font-size: 1.5em;
      line-height: 1;
      margin-bottom: 1.00em;
    }
    /* line 73, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h4 {
      font-size: 1.2em;
      line-height: 1.25;
      margin-bottom: 1.25em;
    }
    /* line 74, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h5 {
      font-size: 1em;
      font-weight: bold;
      margin-bottom: 1.50em;
    }
    /* line 75, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body h6 {
      font-size: 1em;
      font-weight: bold;
    }
    /* line 76, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body p {
      margin: 0 0 1.5em;
    }
    /* line 77, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body p img.left {
      display: inline;
      float: left;
      margin: 1.5em 1.5em 1.5em 0;
      padding: 0;
    }
    /* line 78, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body p img.right {
      display: inline;
      float: right;
      margin: 1.5em 0 1.5em 1.5em;
      padding: 0;
    }
    /* line 80, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body a {
      text-decoration: underline;
      color: #000099;
    }
    /* line 18, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/compass/stylesheets/compass/utilities/links/_link-colors.scss */
    body a:visited {
      color: #000066;
    }
    /* line 22, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/compass/stylesheets/compass/utilities/links/_link-colors.scss */
    body a:focus {
      color: black;
    }
    /* line 26, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/compass/stylesheets/compass/utilities/links/_link-colors.scss */
    body a:hover {
      color: black;
    }
    /* line 30, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/compass/stylesheets/compass/utilities/links/_link-colors.scss */
    body a:active {
      color: #cc0099;
    }
    /* line 81, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body blockquote {
      margin: 1.5em;
      color: #666666;
      font-style: italic;
    }
    /* line 82, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body strong {
      font-weight: bold;
    }
    /* line 83, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body em {
      font-style: italic;
    }
    /* line 84, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body dfn {
      font-style: italic;
      font-weight: bold;
    }
    /* line 85, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body sup, body sub {
      line-height: 0;
    }
    /* line 86, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body abbr, body acronym {
      border-bottom: 1px dotted #666666;
    }
    /* line 87, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body address {
      margin: 0 0 1.5em;
      font-style: italic;
    }
    /* line 88, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body del {
      color: #666666;
    }
    /* line 89, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body pre {
      margin: 1.5em 0;
      white-space: pre;
    }
    /* line 90, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body pre, body code, body tt {
      font: 1em "andale mono", "lucida console", monospace;
      line-height: 1.5;
    }
    /* line 91, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body li ul, body li ol {
      margin: 0;
    }
    /* line 92, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body ul, body ol {
      margin: 0 1.5em 1.5em 0;
      padding-left: 3.333em;
    }
    /* line 93, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body ul {
      list-style-type: disc;
    }
    /* line 94, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body ol {
      list-style-type: decimal;
    }
    /* line 95, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body dl {
      margin: 0 0 1.5em 0;
    }
    /* line 96, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body dl dt {
      font-weight: bold;
    }
    /* line 97, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body dd {
      margin-left: 1.5em;
    }
    /* line 98, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body table {
      margin-bottom: 1.4em;
      width: 100%;
    }
    /* line 99, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body th {
      font-weight: bold;
    }
    /* line 100, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body thead th {
      background: #c3d9ff;
    }
    /* line 101, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body th, body td, body caption {
      padding: 4px 10px 4px 5px;
    }
    /* line 102, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body tr.even td {
      background: #e5ecf9;
    }
    /* line 103, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body tfoot {
      font-style: italic;
    }
    /* line 104, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body caption {
      background: #eeeeee;
    }
    /* line 105, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body .quiet {
      color: #666666;
    }
    /* line 106, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_typography.scss */
    body .loud {
      color: #111111;
    }
    /* line 22, ../../../sass/blog.sass */
    body .two-col {
      width: 950px;
      margin: 0 auto;
      overflow: hidden;
      *zoom: 1;
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding: 1em;
      background-color: #cccccc;
    }

    /* line 28, ../../../sass/blog.sass */
    #header {
      width: 950px;
      margin: 0 auto;
      overflow: hidden;
      *zoom: 1;
    }
    /* line 30, ../../../sass/blog.sass */
    #header #logo {
      background-color: #b3b3b3;
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding-left: 1em;
      margin: 20px;
      display: inline;
      float: left;
      margin-right: 10px;
      width: 710px;
    }
    /* line 139, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_grid.scss */
    * html #header #logo {
      overflow-x: hidden;
    }

    /* line 37, ../../../sass/blog.sass */
    #header, #footer {
      display: inline;
      float: left;
      margin-right: 10px;
      width: 950px;
    }
    /* line 139, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_grid.scss */
    * html #header, * html #footer {
      overflow-x: hidden;
    }

    /* line 39, ../../../sass/blog.sass */
    #content {
      display: inline;
      float: left;
      margin-right: 10px;
      width: 750px;
    }
    /* line 139, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_grid.scss */
    * html #content {
      overflow-x: hidden;
    }

    /* line 41, ../../../sass/blog.sass */
    #sidebar {
      display: inline;
      float: left;
      margin-right: 0;
      width: 190px;
    }
    /* line 139, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/blueprint/stylesheets/blueprint/_grid.scss */
    * html #sidebar {
      overflow-x: hidden;
    }

    /* code fragments */
    /* line 49, ../../../sass/blog.sass */
    pre code {
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      font-family: Monaco, monospace;
      background: black;
      overflow: auto;
      padding: 10px;
      margin-left: 0em;
      margin-right: -180px;
      margin-bottom: 2em;
    }

    /* line 59, ../../../sass/blog.sass */
    #links {
      -moz-box-shadow: black -5px 5px 15px 0;
      -webkit-box-shadow: black -5px 5px 15px 0;
      -o-box-shadow: black -5px 5px 15px 0;
      box-shadow: black -5px 5px 15px 0;
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      padding: 1em;
      margin: 1em;
      background: white;
    }

    /* line 66, ../../../sass/blog.sass */
    h1 a, h2 a, h3 a {
      text-decoration: none;
    }

    /* line 69, ../../../sass/blog.sass */
    ul.links {
      list-style: none;
      padding: 0px;
    }
    /* line 11, ../../../../../../usr/lib/ruby/gems/1.8/gems/compass-0.10.5/frameworks/compass/stylesheets/compass/utilities/lists/_bullets.scss */
    ul.links li {
      list-style-image: none;
      list-style-type: none;
      margin-left: 0px;
    }
    /* line 72, ../../../sass/blog.sass */
    ul.links a {
      text-decoration: none;
    }

    /* line 77, ../../../sass/blog.sass */
    #wave {
      -webkit-border-radius: 15px 15px;
      -moz-border-radius: 15px / 15px;
      -o-border-radius: 15px / 15px;
      -ms-border-radius: 15px / 15px;
      -khtml-border-radius: 15px / 15px;
      border-radius: 15px / 15px;
      -moz-box-shadow: black 0 0 15px 0;
      -webkit-box-shadow: black 0 0 15px 0;
      -o-box-shadow: black 0 0 15px 0;
      box-shadow: black 0 0 15px 0;
      border-width: 5px;
      border-color: #1f1f1f;
    }


Note specifically the reuse of border mixins and my own definition of standard_border
which I can mixin to any style. I now have pure semantic markup in my html and pure
style markup in my css with no copy and paste.

        
