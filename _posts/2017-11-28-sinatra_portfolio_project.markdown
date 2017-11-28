---
layout: post
title:      "Sinatra Portfolio Project"
date:       2017-11-28 18:29:53 +0000
permalink:  sinatra_portfolio_project
---

Game Vault Web Application

This blog covers the Sinatra portfolio project I made for learn.co

The Sinatra project tasks you with using ActiveRecord and databases to persist users, and to give those users a collection to interact with. I went with a simple video game collection, as it made sense to me and was easy to put into words. Users can have many consoles and many games. Consoles belong to an user and can have many games. Games belong to an user and belong to a console. With that in place, I started building an application.

Users are first prompted to either sign up or log in.

```
<h1>Welcome to the Game Vault!</h1>
<h3>Please <a href="/signup">Sign Up</a> or <a href="/login">Log In</a> to continue.</h3>
```

From there they can view their own page that links to pages for their consoles or games.

```
<% if flash.has?(:message) %>
  <%= flash[:message] %>
<% end %>

<h1>Welcome to your Game Vault, <%= @user.username %>!</h1>
 <h3>Go to your Console page to see a list of consoles you own, or to add or remove a console from your collection.</h3>
 <h4><a href="/consoles">Your Console Page</a></h4>
 <h3>Go to your Games page to see a list of games you own, or to add or remove a game from your collection.</h3>
 <h4><a href="/games">Your Game Page</a></h4>



<p><a href="/logout">Log Out</a></p>
```

I utilzed Rack::Flash and the gem 'rack-flash3' to provide error messages to users when applicable. During signup, for example:

```
  post '/signup' do
    @user = User.new(username: params[:username], password: params[:password])
    if @user.valid?
      @user.save
      session[:id] = @user.id
      redirect "/users/#{@user.id}"
    else
      flash[:message] = "Error! Neither Username nor Password cannot be empty!"
      redirect '/signup'
    end
  end
```
The rest is basic CRUD. Users can create new consoles. They can see those consoles on a list of ones that they own. They can rename a console if they so chose, and can remove a console from their collection. They have the same tasks for games, however when they create a game, you assign it to a console that you currently own.

```
<% if flash.has?(:message) %>
  <%= flash[:message] %>
<% end %>

<h3>Congratulations on your new game! Add it to your Vault!</h3>
<form action="/games" method="POST">
	<input type="text" name="name" placeholder="Game name:">
  <br></br>
  <label>Which console did you purchase this game for?</label>

  <br></br>

   <% Console.all.each do |console| %>
     <% if console.user_id == @current_user.id %>
     <input type="checkbox" name="console_id" id="<%=console.id%>" value="<%=console.id%>"><%=console.name%></input>
   <%end%>

     <% end %>

  </ul>
<br></br>
	<input type="submit" id="submit" value="Add to Vault!">
</form>

```

This simple web app handles all the tasks neccesary to maintain a well-organized game collection.

Thanks!

Randall Robinson
