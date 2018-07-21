---
layout: post
title:      "Rails Jquery App Portfolio Project"
date:       2018-07-21 17:26:33 +0000
permalink:  rails_jquery_app_portfolio_project
---

## Updating The Game House with some AJAX!

Today's post is going to go over the processes I took to update my rails app with some jquery/AJAX requests to make the app run smoother. I took some basic functions of the app and used AJAX requests to make them appear without locking the browser, making the app feel more streamlined.

One of the things I loved about the whole process was going deep into Javascript object models. They are sort of similar to Ruby's classes, so it felt natural to process our Javascript code this way.

First, let's start with the contructor function for one of our objects, the guides:

```
function Guide(attributes){
  this.title = attributes.title
  this.id = attributes.id
  this.game_id = attributes.game_id
  this.user_id = attributes.user_id
}
```

This function simply takes in a object of attributes and assigns those to when creating a new Guide object.

We can then build the rest of the functions we need off of this JS model object:

```
Guide.showGuides = function(e){
  e.preventDefault()
  //$.get(this.href).done(Guide.success)
  fetch(this.href).
    then(response => response.json()).
    then(json => Guide.success(json))
}

Guide.newGuide = function(e){
  e.preventDefault()
  var $form = $(this)
  var action = $form.attr("action")
  var params = $form.serialize()
  $.ajax({
    url: action,
    data: params,
    datatype: "json",
    method: "POST"
  }).
  done(Guide.success).
  fail(Guide.newGuideFailure)
}

Guide.showEventTrigger = function(){
  $(".show_guides").on("click", "a.show_guides_link", Guide.showGuides)
}

Guide.newEventTrigger = function(){
  $(".new_guide").on("submit", Guide.newGuide)
}

$(function(){
  Guide.showEventTrigger()
  Guide.newEventTrigger()
})
```

The last function shown there is the call to document.ready, that triggers two seperate functions. Both of those functions are click events that will trigger a call to an AJAX function when their respective elements are clicked.
Guide.newGuide() is the function that is called when a new guide is submitted to our form on our games/show page.
That function sends an AJAX POST request to our server to create a new guide for whatever game's page we are on at the time. Guide.showGuides() is triggered both on our user/show page and our games/index page-after a game is shown to the user via AJAX- as a way to show a list of guides that a user or game has.

Tying these together is our Guide.success() function:
```
Guide.success = function(json){
  var $ol = $("div.show_guides ol")
  $ol.html("")
  for(const newGuide of json.guides){
    var guide = new Guide(newGuide)
    var guideLI = HandlebarsTemplates['guides/show']({guides: guide})
    $ol.append(guideLI)
  }
  document.getElementById("new_guide").reset();
}
```

Here, we have the response of our AJAX requests that we iterate through and construct a new Guide object for each guide. (`var guide = new Guide(newGuide)`) Then, via our handlebars template, append that info to the dom via an already assigned ol/li  HTML object. This works for both AJAX requests that we use because the info we get back from each request is the same and where we are putting it into the dom is the same(that ol/li object) no matter which page we are on.

To finish it out is our failure function for the AJAX POST request. This will fire if the new guide that a user submits via the form is not complete, thereby triggering our ActiveRecord errors:


```
Guide.newGuideFailure = function(json){
  var errors = $.parseJSON(json.responseText).errors
  var $ul = $("div.guide_errors ul")
  $ul.html("")
  for(const singleError of errors){
    $ul.append(singleError)
  }
}
```
This function grabs the errors off of the response and appends them to the dom for the user to see.

Creating these add-ons to this app was a lot of fun, and I hope my code is readable for all those interested!

Thanks!

