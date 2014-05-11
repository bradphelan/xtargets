--- 
title: Who needs Cucumber?
date: 12/05/2011
layout: post
--- 

I've played with cucumber and honestly it doesn't work for me. It's the
complete opposite of DRY. It's RYO ( repeat yourself often ). The thinking
behind it is to get readable specs for your clients. However with a bit
of refactoring you can make capybara rspec based integration tests
look beautiful.

Here is a simple integration spec I wrote to test some tags functionality

~

    require 'spec_helper'

    describe "Tags" do

      before do
        @post1 = Factory.create :post, :tag_list => 'tag_a,tag_b,tag_c'
        @post2 = Factory.create :post, :tag_list => 'tag_a,tag_b'
        @post3 = Factory.create :post, :tag_list => 'tag_a'
      end

      include PostPageHelpers

      it "should show all articles when no tags are clicked" do

        visit posts_path

        within post_tags_section @post1 do
          page.should have_content "tag_a"
          page.should have_content "tag_b"
          page.should have_content "tag_c"
        end

        within post_tags_section @post2 do
          page.should have_content "tag_a"
          page.should have_content "tag_b"
          page.should_not have_content "tag_c"
        end

        within post_tags_section @post3 do
          page.should have_content "tag_a"
          page.should_not have_content "tag_b"
          page.should_not have_content "tag_c"
        end

      end


      it "should filter based on tags" do

        visit posts_path

        within post_tags_section(@post1) do
          click_on "tag_a"
        end

        page.should have_post @post1 
        page.should have_post @post2 
        page.should have_post @post3 

        within post_tags_section(@post1) do
          click_on "tag_b"
        end

        page.should have_post @post1 
        page.should have_post @post2 
        page.should_not have_post @post3 

        within post_tags_section(@post1) do
          click_on "tag_c"
        end

        page.should have_post @post1 
        page.should_not have_post @post2 
        page.should_not have_post @post3 
      end
    end

The trick is to factor out the messy css selector
garbage into helper methods with meaningful names.
The implementation of the helper is below

    module PostPageHelpers
      def have_post(post)
        have_css "section.post_#{post.id}"
      end

      def post_tags_section(post)
        "section.post_#{post.id} .tags"
      end
    end
