--- 
title: Autofill timezone selects with the browser timezone
date: 09/02/2012
--- 

Setting timezones in forms is a drag. Making it a little easier on the user lowers
the friction in your website and makes it more likely users will **stick**. Here
is a simple technique for autofilling timezone selects using formtastic and a little
coffeescript / jquery.

First create yourself a timezone class and a method for generating a mapping
table between timezone offsets and the name of the timezone as used by rails.

    class OpenKitchen
      class TimeZone
        # Returns a hash with javascript compatible timezone
        # offset  as key.
        def self.timezones_by_offset
          @timezone_by_offset = {}
          ActiveSupport::TimeZone.all.each do |tz|
            unless @timezone_by_offset.has_key? tz.utc_offset
              @timezone_by_offset[tz.utc_offset/60] = tz.name
            end
          end
          @timezone_by_offset
        end
      end
    end


then create a coffeescript file named

  /app/assets/javascripts/timezone.js.coffee.erb

Note the **erb** ending. This is very important because we are going
to generate the coffeescript code using the above OpenKitchen::TimeZone
class as a helper

    <%  require_dependency 'openkitchen/timezone' %>

    class Timezone

      # Table of timezones ( Generated by OpenKitchen::TimeZone class )
      @table: <%= OpenKitchen::TimeZone::timezones_by_offset.to_json %>

      # Find first entry with matching timezone offset
      @lookup_by_offset: (_offset)->
        _offset =  _offset.toString()
        (tz for offset, tz of @table when _offset == offset)[0]

      # Return the guessed timezone for this browser
      @timezone: ->
        x = new Date
        @lookup_by_offset(-x.getTimezoneOffset())

    $(document).ready =>

      # Find timezone selected marked for autofill
      tz_select = $(".time_zone select")
      $("select.timezone.autofill").val(Timezone.timezone())

~

Note the selector is conditional on an **autofill** class being
present. We don't want to just reset all timezone selects if
they have been manually set by the user.

In our formtastic code we can now write

    = semantic_form_for @widget do |f|
      = f.inputs do
        - autofill = @widget.timezone ? nil : 'autofill'
        = f.input :timezone, :as => :time_zone, 
                             :input_html => disabled_hash, 
                             :input_html => { :class => "timezone #{autofill}" }

Now when we create a new form that requires a timezone to be entered then
the select will default to the timezone of the browser. This may not be
the correct one and the user has the option of changing the option.


Happy timezones.

  

  





