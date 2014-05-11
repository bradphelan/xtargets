--- 
title: Relative path names
date: 23/03/2010
layout: post
--- 

Who is sick of

    File.expand_path("../foo.rb", __FILE__)

to get the path of foo.rb relative to the directory that the current file is in.
How about

    ThisFile / "foo.rb"

Much nicer.

Implementation.

    class ThisFile

      private
        CALLER_RE = /(.*):([0-9]+)(:in \'(.*)')?/
        def self.parse_backtrace(backtrace)
          backtrace.collect do |c|
            CALLER_RE.match(c)[1]
          end
        end

      public
      def self.__file__(n=0)
        begin
          raise "foo"
        rescue Exception => e
          parse_backtrace(e.backtrace)[n]
        end
      end

      def self.expand_path( path )
        File.expand_path( path, File.dirname(ThisFile.__file__(2)))
      end

      def self./ (path)
        File.expand_path( path, File.dirname(ThisFile.__file__(2)))
      end

    end


Spec

    describe RelativeFile do
      it "should be able to get the caller file" do
        ThisFile.__file__ == __FILE__
      end

      it "should be able to find a file relative to this one" do
        ThisFile.expand_path("foo.rb").should == File.expand_path("../foo.rb", __FILE__)

        # An operator version of this for happiness
        (ThisFile / "foo.rb").should == File.expand_path("../foo.rb", __FILE__)
      end
    end

Source  at http://github.com/bradphelan/thisfile

Also available at http://rubygems.org/gems/thisfile
    


    
