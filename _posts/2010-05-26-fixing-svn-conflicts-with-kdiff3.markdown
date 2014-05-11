--- 
title: Fixing SVN conflicts with kdiff3
date: 26/05/2010
layout: post
--- 

Git has hall the niceness baked in for fixing conflicts with kdiff3

    git mergetool --tool kdiff3

and you are away. svn in lacking in these regards. The below script 
automates some of this process.

fixconflicts.rb

    #!/bin/ruby
    `svn status | grep ^C`.each_line() do |file|
        file.gsub!(/^C\s*/,"").strip!
        leftright = `ls #{file}.r*`.split.collect { |c| c.strip }
        versions = leftright.sort
        left = versions[0]
        right = versions[1]
        working = "#{file}.mine"
        cmd = "kdiff3 -m --auto -o #{file} #{left} #{right} #{working}"
        puts cmd
        puts `#{cmd}`
        puts "Resolve y/n"
        yn = gets
        if yn =~ /y/
            `svn resolved #{file}`
        end
     end

Run the script from your sandbox root and it will load kdiff3 to fix
all of your conflicts from the merge. Pretty simple but it makes
my life a lot easier.


    



