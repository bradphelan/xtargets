--- 
title: validating collection size on has many through relations in rails3
date: 13/02/2011
--- 

If I have the following model


    user -----5 course_registration *------ course


ie a user can register for 5 courses and a course can have an unlimited
number of students. I want to do this with Rails validations. The below
outlines some best practise and explains how to collect the validation
errors when they occur.

~

I then define the following models

    class Course < ActiveRecord::Base
      has_many :course_registrations
      has_many :students, :class_name => 'User', :through => :course_registrations, :source => :user
    end

    class User < ActiveRecord::Base
      has_many :course_registrations
      has_many :courses, :through => :course_registrations
    end

    class CourseRegistration < ActiveRecord::Base

      belongs_to :course
      belongs_to :user

      # These join attributes should be immutable
      attr_readonly :course_id, :user_id

      validates_presence_of :course_id
      validates_presence_of :user_id

      MAXIMUM_COURSES = 5

      validate :on => :create do
        if user.course_registrations.size >= MAXIMUM_COURSES
          errors.add :user, "can only apply for #{MAXIMUM_COURSES} courses. Please remove one."
        end
      end

    end

The above is the key reason for using _has many through_ instead of using _has and belongs to many_.
HABTM uses an implicit join model whereas HMT you have to be more explicit but you then have the
flexibility to do smarter validations on the collection relation.

You may have been tempted to solve such a problem with after\_add callback on the user model. However
that doesn't cover all use cases of creating the registration instances and you can easily miss
out on the validation.

A thing to note

    @user = User.create 

    @user.courses = 5.times.map { Course.create }  

is ok but

    @user.courses = 6.times.map { Course.create }  

will throw an exception and so will

    @user.create :courses => ( 6.times.map { Course.create } )

even though you use _create_ and not _create!_. The exception has been thrown
from the implicit creation of CourseRegistration objects that associate the
User and Course objects.

Therefore you will always need to rescue exceptions in your controllers
if you are doing anything like the above rather than just check the boolean
of @user.save

Note that the current rails_admin gem doesn't handle validation faults in
has many through relations and produces a backtrace. I've created a fork
at

https://github.com/bradphelan/rails_admin

which fixes the problem.


