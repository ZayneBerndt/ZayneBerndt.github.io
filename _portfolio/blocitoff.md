---
layout: post
title: knocitoff
thumbnail-path: "img/blocitoff.png"
short-description: A self-destructing to-do list application.

---

{:.center}
![]({{ site.baseurl }}/img/tick.jpeg)

## Explanation

To-do lists are notorious for collecting junk: to-do items that you want to remember, but are not very important and thus get consistently put off. To address the problem of to-do list clutter, I have developed knocitoff.

Knocitoff will aim to keep to-do lists manageable by automatically deleting to-do items that have not been completed after seven days. The hypothesis is that if the to-do item is not important enough to be completed in seven days, it doesn't belong on your to-do list.


## Challenge
Being only my second app ever developed I wanted to cement functionality learnt previously from building <strong>[Blocipedia](../blocipedia/)</strong>. I needed to focus on Ruby and Rails syntax and concentrate on developing an aptitude for bug fixing.



## Solution

<strong>1. Creating user authentication.</strong>

 I used the "Devise gem" that is a flexible authentication solution for Rails with Warden. Devise abstracts away many of the gritty details of building a custom authentication system.

 Below I created a user modal

```ruby
class User < ActiveRecord::Base
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :confirmable

  has_many :items
end
```
Some basic navigation if user is signed in

```ruby

 #check if user signed in, if they are display user.email and signout btn
 <% if user_signed_in? %>
   Signed in as: <strong><%= current_user.email %></strong> |
   <%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> - <%= link_to "Sign Out", destroy_user_session_path, method: :delete, :class => 'navbar-link'  %>
 <% else %>
 # display sign in and sign out btns if not signed in.
   <%= link_to "Sign Up", new_user_registration_path, :class => 'navbar-link'  %> -
   <%= link_to "Sign In", new_user_session_path, :class => 'navbar-link'  %>
 <% end %>

```
Most of the features of an application that uses Devise are only accessible if the user is signed in. That means that being able to test those features required me to mock/fake the sign in process first. Once I have a signed in user, I could then execute the actual test.

```ruby
RSpec.configure do |config|
    ...
    config.include Devise::Test::ControllerHelpers, type: :controller
    config.include Devise::Test::ControllerHelpers, type: :view
    ...
  end
```
<strong>2.  Create the items. </strong>

The items were important as they had to reflect association with the user and of course had the two actions of "create" and "destroy". Below is both the Items Controller and User Controller demonstrating association with each other.
```ruby
class ItemsController < ApplicationController
before_action :authenticate_user!

  def create
    @item = Item.new(item_params)
    @item.user = current_user
  if @item.save
    flash[:notice] = "Item was saved."
  else
    flash[:error] = "Error creating Item. Please try again."
  end
  redirect_to current_user
end

  def destroy
  @item = Item.find(params[:id])
    # prevent users from deleting other users tasks
    if current_user != @item.user
      flash[:error] = "You do not have the authority to do that task"
    elsif @item.destroy
      flash[:notice] = "Congratulations on completing the task!"
    else
      flash[:error] = "Something seems to have gone wrong. Please try again."
  end
    redirect_to current_user
end

  def update
  @item = Item.find(params[:id])
    if current_user != @item.user
      flash[:error] = "You do not have the authority modify that task"
    elsif @item.attributes.has_key?(params[:attribute])
      @item.update_attributes(params[:attribute] => Time.now)
      flash[:notice] = "Congratulations on completing the task!" if params[:attribute] == "completed_at"
      flash[:notice] = "This task has been reinstated" if params[:attribute] == "created_at"
    else
      flash[:error] = "Something seems to have gone wrong. Please try again."
    end
    redirect_to current_user
end
  private
  def item_params
    params.require(:item).permit(:name)
  end
end
```
<strong>3. Display time remaining and an expiration method</strong>

```ruby
class Item < ActiveRecord::Base
  belongs_to :user

  default_scope { order('updated_at DESC')}

  def expired?
    remaining = (created_at - 7.days.ago).ceil

    if remaining < 0
      true
    else
      false
    end
  end
end
```


## Results

<img src="/img/blocitoff-desktop.jpg">


## Languages and Tools used

Ruby, Rails, PostgreSQL, HTML5, CSS, Javascript, AJAX
