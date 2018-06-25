---
layout: post
title:      "Breaking Down My Sinatra App"
date:       2018-06-25 05:35:15 -0400
permalink:  breaking_down_my_sinatra_app
---


I was a bit overambitious in my initial plans for my Sinatra app. There were so many features I wanted it to include, but I had to take multiple steps back and make sure that I simply satisfied the requirements for the project. 

## The Premise: 

My app is based off a site I created several years ago called [Eats Abroad](https://eatsabroad.com). I interviewed renowned chefs worldwide about their favorite places to eat and drink in their cities. 

For my Sinatra project, I decided to play off of this and create an app where chefs can submit their own restaurant recommendations. Once signed up and logged in, they can view all of the recommended restaurants on the site. They can add favorite restaurants to their own list by either choosing from the existing list of restaurants in the database or adding a new one. In doing so, they would also have to leave comments about those restaurants, and in turn, chefs can edit or delete their own comments. If a comment is deleted, that restaurant is also removed from the chef’s list of favorite restaurants, given the chef’s “has many” restaurants through comments association.

## The Models:

Ultimately I ended up with four models:
-	Chef: the “user” model with attributes of name, email, and password. A chef has many comments and has many restaurants through comments;
-	City: simply has a name attribute. A city has many restaurants, as well as many chefs through restaurants;
-	Restaurant: has name and cuisine attributes. A restaurant belongs to a city, and it has many comments through chefs;
-	Comment: has a content attribute. A comment belongs to both a chef and a restaurant, and this ultimately serves as the join table.

I grappled with whether to make Comment its own model, or if it should be one of Restaurant's attributes. I ultimately decided to make it its own model because Comment was going to be the only attribute that a Chef could edit or delete. (Chefs wouldn’t be able to update a restaurant’s name or cuisine once it’s in the database, nor would they be able to delete a restaurant from the database. In my mind, that would be the responsibility of an admin-type figure.)

I started out the project by creating my models with their appropriate associations and then migrating my databases (and subsequently creating a number of migrations to add/edit/delete columns). 

## The Controllers:

Though I had four models, I needed controllers for just three. Originally I wanted chefs to be able to add a city, but I took that functionality out since it wasn’t necessary for the project. I kept the city views simple and just had all of the restaurants appear in /restaurants, listed out by city. Therefore, my City class didn’t need a controller. 

As the user model, Chef has a controller, responsible for signing up, logging in, and showing a chef’s restaurant list. 

The Restaurant controller is responsible for creating and showing restaurants. 

The Comment controller includes routes for editing, updating, and deleting a comment. 

I initially had the Comment editing function incorporated in the Restaurant controller since a chef is expected to add a comment when adding a restaurant to their list. However, I had difficulty writing the edit route correctly. Any given restaurant could have more than one comment from different chefs, and I was having trouble getting the comment I wanted to edit assigned to my @comment instance variable.

After Slacking with Cernan about my issue, he suggested creating a Comment controller. This made complete sense since I wasn't giving the user the ability to edit or delete a restaurant. Comment required its own controller for those tasks. I guess I had wanted to reduce the number of controllers in my app, but I learned that in this case, less isn’t always more! 

## CRUD Actions

As mentioned above, my Comment class is the one that belongs to both a chef and a restaurant, and it’s capable of being created, read, edited, and deleted. 

The creation and read routes actually exist in the Restaurant controller because of the way I set up the “add restaurant” form. From a UX standpoint, it made sense to me that the "add restaurant" form would ask the user to both add a restaurant and a comment about it at the same time. 

The editing and deleting routes are in the Comment controller. 

In addition, an individual chef’s restaurants can be read at /chefs/:id. 

## Validating Data and Error Messages 

In this lab, I used validations in the User class to confirm uniqueness of a chef’s email address and the presence of a name. I also used the has_secure_password macro to validate the password.

I added Rack Flash to my app to handle error and confirmation messages. I wanted messages to appear at the top of new, edit, and delete pages. These messages would confirm changes, or alert the user that they didn’t have permission to update or delete comments that weren’t theirs. 

I also have messages appearing if a user doesn't sign up or log in properly. For example, if a chef tries to sign up with an email that's already in the database, they'll be shown an error message saying that that email address already exists. 

## Final Thoughts

I feel like I have a really strong grasp of routes and sessions now, and I had a lot of fun building this app. Presentation-wise, it's definitely nowhere near what I envisioned, but I'm looking forward to the Rails project and possibly building off this premise again and really fleshing it out in terms of design and functionality. 
