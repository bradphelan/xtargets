--- 
title: Solving polymorphic has_one through building and nested forms
date: 04/04/2012
layout: post
--- 

I recently hit on the brilliant idea of using __STI__ (single table inheritence),
__polymorphic classes__, __nested forms__ and __has_one through__ models all at once just
to prove how silly I am.

My use case is an asset manager backed by paperclip. I wanted all assets
to go into a single S3 folder and be mananged by a single :assets table.
Then I wanted __any__ other model to be able to associate uploaded assets
at any arity. Gumpf!

~

My base class for an STI compliant asset table is

    # == Schema Information
    #
    # Table name: assets
    #
    #  id                      :integer         not null, primary key
    #  attachment_file_name    :string(255)
    #  attachment_content_type :string(255)
    #  attachment_file_size    :integer
    #  attachment_updated_at   :datetime
    #  created_at              :datetime        not null
    #  updated_at              :datetime        not null
    #  type                    :string(255)
    #  attachment_processing   :boolean
    #  terminated              :boolean         default(FALSE)
    #

		class Asset < ActiveRecord::Base
			belongs_to :assetable, :polymorphic => true
			delegate :url, :to => :attachment
			delegate :expiring_url, :to => :attachment
			delegate :present?, :to => :attachment

			validates_attachment_presence :attachment

			has_many :assetable_assets, :dependent => :destroy
			has_many :assetables, :through => :assetable_assets, :as => :assetable

			AssetPath = ":rails_env/assets/:id/:style.:extension"

			def self.configure_attachment options = {}
				options.merge! :path => AssetPath
				has_attached_file :attachment, options
			end

		end

Then I need a class to associate other objects, __assetables__, with uploaded
assets. Note that __assetables__ are polymorphic

		# == Schema Information
		#
		# Table name: assetable_assets
		#
		#  id             :integer         not null, primary key
		#  assetable_id   :integer
		#  assetable_type :string(255)
		#  asset_id       :integer
		#  created_at     :datetime        not null
		#  updated_at     :datetime        not null
		#

		class AssetableAsset < ActiveRecord::Base
			belongs_to :assetable, :polymorphic => true
			belongs_to :asset

			# Clean up orphans
			before_destroy do
				if not asset.nil?
					if asset.assetable_assets.count == 1
						asset.background_destroy
					end
				end
			end
		end

And an example of a concrete __assetable__ class.

	# == Schema Information
	#
	# Table name: users
	#
	#  id                     :integer         not null, primary key
	#  email                  :string(255)     default(""), not null
	#

	class User < ActiveRecord::Base

		has_one :assetable_asset, :as => :assetable, :dependent => :destroy
		has_one :avatar, :source => :asset, :through => :assetable_asset, :class_name => "ImageAsset"
		attr_accessible :avatar_attributes
		accepts_nested_attributes_for :avatar

		def build_avatar attributes={}, opts={}
			asset = ::ImageAsset.new attributes, opts
			build_assetable_asset :asset => asset
			self.avatar = asset
		end

		def avatar_with_build
			a = avatar_without_build
			unless a
				return build_avatar
			end
			a
		end
		alias_method_chain :avatar, :build
	end

Now I can upload an avatar via a formtastic form as so

    = semantic_form_for @user, :url => user_profile_path(@user), :html => { :multipart => true } do |f|
			= f.inputs do 
				.span5
					= f.input :email
					= f.input :name

					- if user.avater.present?
						= image_tag user.avatar.url(:thumb)

					= f.inputs :attachment, :for => :avatar do |a|
						= a.input :attachment, :as => :file
			= f.buttons

For some reason that I can't get my head around Rails doesn't ship
with a handler for polymorphic has_one builders. I expected to be 
able to do

		user.build_avatar

out of the box but I got an error that the method does not exist and
it took some fiddling to figure out how to emulate it.

For reference, The image asset class is defined below which fleshes
out the STI part of the problem. Maybe it would not be necessary to
have STI support on the assets table but it might happen that I 
have assets with different styles and need to classify them
as such.

	class ImageAsset < Asset

		configure_attachment styles: ImageSizes.standard_sizes_hash

		validates_attachment_content_type :attachment, 
			:content_type => %r{image/.*}, 
			:less_than => 5.megabyte

	end
