---
layout: post
title:      "Turning My Sinatra App Into a Rails App"
date:       2018-08-10 08:43:52 +0000
permalink:  turning_my_sinatra_app_into_a_rails_app
---


For my Rails project, I continued working off of my Eats Abroad Sinatra app.

I have to admit that Rails was a bit of a challenge for me. The "magic" of it all was at times overwhelming, and I found myself re-reading the lessons and labs more often than I had in previous sections. I loved how smart  and powerful Rails was, but at the same time, I'd often find myself asking, "But how?!" 

With this Rails project, because I was falling behind on my cohort's schedule (no thanks to a 3-week-long holiday in Australia and New Zealand), I stuck with the basic requirements to pass. Nothing fancy here. As much as I wanted to integrate the edit and update functions for a comment and attempt to make the app in general look a lot nicer, I knew I could wait until my next project to tackle those items.

# The Basics 
With this project, I again had the same models and structure as my Sinatra app:
1. Chef model (essentially the User): has many comments and has many restaurants through comments;
2. Restaurant model: belongs to a city, has many comments, and has many chefs through comments;
3. Comment model: belongs to a chef and belongs to a restaurant (the join table);
4. City model: has many restaurants

# Nested Routes 
I incorporated nested routes for chefs, cities, and restaurants. 

If you visit the cities index, you see a list of the four cities currently in the database: Chicago, Vancouver, Boston, and Dublin. When you click on one of them, you’re sent to its restaurants index page, nested within the city’s show page. I also added a requirement that a user must be logged in to see any of the restaurant pages. 

Similarly, if you visit a chef’s show page, you can see his/her restaurants at chefs/:id/restaurants. I added various conditions so that the headline would be different depending on whether the chef’s show page is yours or another chef’s. If you are on your own chef’s page, a link appears that directs you to the form to add a new restaurant to your recommendations list (/restaurants/new).

The restaurants/new route shows two forms. The first lets a chef select an existing restaurant from the database to add to their list. The second allows the chef to add a brand new restaurant.

These forms were probably the trickiest features to implement because I wanted to add a bunch of conditions. I wanted to make sure the drop-down menu didn’t include restaurants already recommended by the chef. I also didn’t want the database to have duplicate restaurant entries in case a chef added a brand new restaurant that was actually already in the database. In addition, I had to make sure the forms properly re-rendered with errors if a restaurant name and/or city weren’t entered. My restaurants controller is a bit heavier than I’d like it to be, but the code at least works!

# Class Scope Method
I chose to sort the restaurants by cuisine. I created a route called /restaurants/cuisine/:type, and wrote a class method in Restaurant called #by_cuisine(cuisine). 

Through testing, I found that it mattered whether the argument passed into ‘cuisine’ was uppercase or lowercase. Since there’s no knowing whether a chef would properly capitalize a cuisine’s name when adding a new restaurant, I created a #capitalize_cuisine before_validation method in the Restaurant class. That way, whatever was passed in as a restaurant’s cuisine would automatically be capitalized and saved into the database as such. 

# Partials 
A couple of my forms screamed for partials. The chef’s new and edit forms (for their profiles) were essentially identical, so the meat of those form_fors were thrown into a partial.

My restaurants index form was fairly messy because of all the if/else conditions I had (since it served as a nested route for several routes). The @restaurants iteration kept being repeated in every condition, so I created a partial for that and saved myself a few lines of code. 

# Final Thoughts 
Working on this app definitely reinforced my knowledge of Rails, especially forms and validations. I want to focus more on building upon that knowledge and understanding more of the "magic," as well as working on ways to code more eloquently.


