---
layout: post
title:      "Rails Portfolio Project"
date:       2018-05-05 17:51:08 +0000
permalink:  rails_portfolio_project
---


### The Guide House

This post goes over my portfolio project for rails, The Guide House. The Guide House(TGH) was loosely based on the website *www.gamefaqs.com*  which I have used frequently throughout my life as a resource for knowledge and guides on video games. The requirements for the project helped me narrow down what kind of relationships I wanted, and using video game guides as the basis made the relationships easier to understand.  Users have many guides and have many games through those guides. Games can have many guides and have users through those guides. A guide belongs to a user and a game.


TGH has a basic user flow of being able to sign up with a regular user name and password, or being able to login via 3rd party(in this case, GitHub). 

Some of the coding required to set up OmniAuth was difficult to implement, as I had not had extensive experience with that gem. Our User model had to be able to find a user that had already used OmniAuth to sign in, or if they were new, use OmniAuth to sign them up via GitHub. I used the following method to implement this:

```
def self.find_or_create_by_omniauth(auth_hash)
  oauth_name = auth_hash["info"]["nickname"]
  if user = User.find_by(:user_name => oauth_name)
    return user
  else
    user = User.create(:user_name => oauth_name, :password => SecureRandom.hex)
    return user
  end
end
```
This method is used in our sessions controller:

```
def create
    if auth_hash = request.env["omniauth.auth"]
      user = User.find_or_create_by_omniauth(auth_hash)
      session[:user_id] = user.id
      redirect_to user_path(user.id)
    else
      user = User.find_by(:user_name => params[:user][:user_name])
        if user && user.authenticate(params[:user][:password])
        session[:user_id] = user.id
        redirect_to user_path(user.id)
      else
        @user = User.new
        flash[:notice] = "User not found, Please try again."
        render 'sessions/new'
      end
    end
  end
```
 This method will log users in via OmniAuth if the correct info is present, or attempt to log users in via regular means otherwise. There is also the redirect if we cannot find the user given the info provided.
 
 Once logged in, users can search for a specific game, or create a new one and then find a guide for that game, or create one of thier own. Much of the code for these is very ordinary for this type of app. I did want to highlight the search form that I used to search for the games.
 
 The logic in our Game model:

```
  def self.search(search)
    if search
      where('title LIKE ?', "%#{search}%")
    else
      all
    end
  end
```

which we use in the games controller:

```
  def index
    @games = Game.search(params[:search])
  end

```
to either get the info provided by the form:

```
  <%= form_tag games_path, method: :get do %>
    <%= text_field_tag :search, params[:search] %>
    <%= submit_tag "Search", name: nil %>
    <% end %>
```
or return all of our games if no search info is provided. We use @games variable and iterate over it to get our list of games in our games/index.

Thanks for taking the time to look at my app. Hopefully, it is easy to follow and understand!
