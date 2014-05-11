--- 
title: Automagically create a style guide with SASS that will make you and your designer happy
date: 19/05/2011
--- 

Communication with a designer in your project is not always easy. They work with Adobe
Illustrator. You work with HTML and SASS. It would be nice to have a middle ground where
you can quickly confirm styles and layouts without resorting to full application examples.
We have created a style guide which we automatically generate by parsing some simple SASS
files that we enforce naming conventions on.

~

The page we generate is something like below

![Style Guide](https://img.skitch.com/20110519-mu3wc8fey9m3775n1j66a512un.jpg)

Part of the haml template that generates the above is below

      %dl
      - XTargets::Sass.swatches.each do |c|
        %dt .#{c}
        %dd{:class=>"#{c} character-transparent"}
          xxx

The above uses a simple parser to extract the style names from the below SASS file

    //////////////////////////////////////////////////////////////////////////////////
    // Color Variables
    //////////////////////////////////////////////////////////////////////////////////

    //////// Framework Colors - DO NOT CHANGE
    $color-transparent     : rgba(0  , 0   , 0    , 0)
    $color-white           : rgb(255 , 255, 255)
    $color-black           : rgb(0   , 0   , 0)

    //////// Designer Defined Colors
    $color-light-blue      : rgb(230 , 230 , 255)
    $color-egg             : rgb(203 , 219 , 231)
    $color-dark-grey       : rgb(227 , 228 , 208)
    $color-light-grey      : rgb(85  , 85  , 85 )
    $color-maroon          : rgb(153 , 153 , 153)
    $color-royal-blue      : rgb(157 , 11  , 14 )
    $color-text-callout    : rgb(0   , 110 , 165)
    $color-pale-olive      : rgb(204 , 204 , 204)

    //////////////////////////////////////////////////////////////////////////////////
    // Flat Background Colors
    //////////////////////////////////////////////////////////////////////////////////
    .color-background-black
      background-color: $color-black

    .color-background-white
      background-color: $color-white

    .color-background-light-blue
      background-color: $color-light-blue

    .color-background-egg
      background-color: $color-egg

    .color-background-dark-grey
      background-color: $color-dark-grey

    .color-background-light-grey
      background-color: $color-light-grey

    .color-background-maroon
      background-color: $color-maroon

    .color-background-royal-blue
      background-color: $color-royal-blue

    .color-background-text-callout
      background-color: $color-text-callout

    .color-background-pale-olive
      background-color: $color-pale-olive

I enforce a naming convention so it's easy to extract the style names
and generate a style guide page. The parser is a very simple regexp
filter

    module XTargets
      class Sass
        SASS_DIR = File.expand_path "../../../app/stylesheets/partials/", __FILE__
        SWATCH_FILE = File.join SASS_DIR, "_swatches.sass"

        # Yields
        def self.swatches
          style SWATCH_FILE, 'color-background'
        end

        def self.style file_name, prefix_pattern
          File.open(file_name).each_line.select do |line|
            line =~ /^\.#{prefix_pattern}/
          end.collect do |line|
              line[1..-1].chomp
          end
        end

      end
    end

