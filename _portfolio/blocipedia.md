---
layout: post
title:  Blocipedia
thumbnail-path: "img/blocipedia.png"
short-description: A production quality SaaS app that allows users to create their own wikis.

---

{:.center}
![]({{ site.baseurl }}/img/1441485.png)

## Explanation

Wikis are a great way to collaborate on community-sourced content. Whether the wiki is for a hobby or work-related project, I have build an app that lets users create their own wikis and share them publicly or privately with other collaborators. As my first ever application I used this project to get familiar with MVC architecture and understand Ruby, Rails, PostgreSQL, HTML and CSS. It was also a great chance to get to grips with github and the command line.

## Problem
Below I wanted to demonstrate areas that I haven't developed in my other apps mainly focusing on demonstrating Test Driven Development and
and functionality that exposed me to greater usage of function syntax.


## Solution

<strong>1. Test Driven Development</strong>

I have learnt the importance of using a TDD approach to development, in the project of the many testing frameworks available I chose to use RSpec due to it being a canonical framework, and thus the most likely framework to encounter as a professional Rails developer.

As with my other model testing, the tests below test for field validation and attributes.

```ruby


require 'rails_helper'

 RSpec.describe User, type: :model do

   let(:user) { User.create!(name: "Bloccit User", email: "user@bloccit.com", password: "password") }
   # Shoulda tests for name
   it { is_expected.to validate_presence_of(:name) }
   it { is_expected.to validate_length_of(:name).is_at_least(1) }

   # Shoulda tests for email
   it { is_expected.to validate_presence_of(:email) }
   it { is_expected.to validate_uniqueness_of(:email) }
   it { is_expected.to validate_length_of(:email).is_at_least(3) }
   it { is_expected.to allow_value("user@bloccit.com").for(:email) }

   # Shoulda tests for password
   it { is_expected.to validate_presence_of(:password) }
   it { is_expected.to have_secure_password }
   it { is_expected.to validate_length_of(:password).is_at_least(6) }

   describe "attributes" do
     it "should have name and email attributes" do
       expect(user).to have_attributes(name: "Bloccit User", email: "user@bloccit.com")
     end
   end
```
Here I have simulated and added tests for an invalid user
```ruby
describe "invalid user" do
     let(:user_with_invalid_name) { User.new(name: "", email: "user@bloccit.com") }
     let(:user_with_invalid_email) { User.new(name: "Bloccit User", email: "") }

     it "should be an invalid user due to blank name" do
       expect(user_with_invalid_name).to_not be_valid
     end

     it "should be an invalid user due to blank email" do
       expect(user_with_invalid_email).to_not be_valid
     end

   end
```
<strong>2. Subscription Algorithm</strong>

Below I created user roles, I wanted there to be a standard, premium and admin role. I also built the subscription feature using the Stripe payment API.

```ruby
class SubscriptionsController < ApplicationController

  def new
    @amount = 15_00
    @stripe_btn_data = {
      key: "#{ Rails.configuration.stripe[:publishable_key] }",
      description: "BigMoney Membership - #{current_user.name}",
      amount: @amount
    }
  end

  def create
    @amount = 15_00

    if current_user.customer_id.nil?
      customer = Stripe::Customer.create(
        email: current_user.email,
        card: params[:stripeToken]
      )
      current_user.customer_id = customer.id
    end

    customer = Stripe::Customer.retrieve(current_user.customer_id)
    return unless customer.subscriptions.data.empty?
    customer.subscriptions.create(plan: 'premium')
    current_user.update_attribute(:role, 'premium')

    flash[:success] = "Your payment has been receieved. Thank you!"
    redirect_to edit_user_registration_path

  rescue Stripe::CardError => e
    current_user.update_attribute(:role, 'standard')
    flash[:error] = e.message
    redirect_to new_subscriptions_path
  end

  def downgrade
    customer = Stripe::Customer.retrieve(current_user.customer_id)

    subscription_id = customer.subscriptions.data.first.id
    customer.subscriptions.retrieve(subscription_id).delete
    current_user.update_attributes(role: 'standard')
    current_user.make_wikis_public
    redirect_to edit_user_registration_path
  end

end
```
```ruby
class User < ActiveRecord::Base
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :confirmable
  has_many :wikis
  has_many :collaborators, through: :wikis

  def standard?
    role == 'standard'
  end

  def admin?
    role == 'admin'
  end

  def premium?
    role == 'premium'
  end

  def make_wikis_public
    wikis.each do |wiki|
      if wiki.public == false
        wiki.update_attributes(public: true)
      end
    end
  end
end
```


## Languages and Tools
Ruby/Rails, RSpec (testing), PostgreSQL, Stripe (payment), HTML5(ERB), Sass, Javascript, jQuery
