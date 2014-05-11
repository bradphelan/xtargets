--- 
title: integrating devise and typus
date: 14/02/2011
layout: post
--- 

Unfortunately [typus](https://github.com/fesplugas/typus) as good as it is has it's own authentication and authorisation framework
that out of the box does not play nice with devise. I'd like only one way of logging into the system, backend or front end.

~

Create the following module which is a modified version of the typus code

    module Typus
      module Orm
        module ActiveRecord
          module User

            def self.included(base)
              base.extend(ClassMethods)
            end

            module ClassMethods

              def enable_as_typus_devise_user
                extend ClassMethodsMixin
                validates :role, :presence => true
                serialize :preferences
                include InstanceMethods

              end

            end

            module ClassMethodsMixin

              def authenticate(email, password)
                resource = find_for_database_authentication({ :email=>email })
                r = resource && resource.valid_password?( password ) ? resource : nil
              end

            end

            module InstanceMethods

              def name
                full_name = []
                if respond_to?(:first_name) && respond_to?(:last_name)
                  full_name = [first_name, last_name].delete_if { |s| s.blank? }
                end
                full_name.any? ? full_name.join(" ") : email
              end

              # This includes lots of stuff we don't need but
              # is easier than copy and pasting
              include Typus::EnableAsTypusUser::InstanceMethods


            end

          end
        end
      end
    end

    module Typus

      module Authentication

        module Session

          # Monkey patch the Session#authenticate to use
          # devise
          def authenticate
            if user_signed_in?
              u = current_user
              session[:typus_user_id] = u.id
            else
              back_to = request.env['PATH_INFO'] unless [
                admin_dashboard_path, 
                admin_path
              ].include?(request.env['PATH_INFO'])

              redirect_to new_user_session(:back_to => back_to)
            end

          end

          def admin_user
            current_user
          end

        end

      end

    end

and mix it in like

    require 'typus_devise'

    class User < ActiveRecord::Base
      
      # Include default devise modules. Others available are:
      # :token_authenticatable, :confirmable, :lockable and :timeoutable
      devise :database_authenticatable, :registerable,
             :recoverable, :rememberable, :trackable, :validatable

      # Setup accessible (or protected) attributes for your model
      attr_accessible :email, :password, :password_confirmation, :remember_me

      belongs_to :role

      public

      #
      # Typus adaptation
      #

      include Typus::Orm::ActiveRecord::User
      enable_as_typus_devise_user

      alias :orole :role
      def role
        orole.name
      end

    end

There are still a couple of gotchas I haven't figured out. 

- The "Sign Out" link on the typus dashboard needs rerouting to the devise sign out.
- I can only handle single roles per user which is not ideal
- The roles have to be returned as strings not role objects
