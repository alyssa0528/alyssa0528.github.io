---
layout: post
title:      "Adding Front-End Magic to My Rails App"
date:       2018-10-10 16:57:01 +0000
permalink:  adding_front-end_magic_to_my_rails_app
---


I was honestly pretty terrified of starting this project. Even though I knew I wouldn’t have to build an app completely from scratch, I was worried about integrating front-end features. Overall, I felt like it took longer for me to understand certain concepts of JavaScript, AJAX, and jQuery compared to Ruby, Sinatra, and Rails. I know for my Rails project blog post, I wrote about how I felt overwhelmed by the magic of Rails, but looking back, that all seems to be nothing compared to the complexities of JS and AJAX!

To prepare, I re-read a bunch of lessons and re-watched videos. I also read through my copy of Jon Duckett’s JavaScript and jQuery book. I made a list of the features I wanted to incorporate, according to the project’s requirements, and I dove in. My JS project is based off of my Rails project. To read more about that and its features, [click here](http://alyssakim.net/turning_my_sinatra_app_into_a_rails_app).

First, I had to create a basic “navigation bar” as this was missing from my Rails portfolio project. I wrote code for a very basic nav bar layout ERB file that included links to the logged-in chef’s profile page, a list to all recommended restaurants, a link to a form where a chef could add a new restaurant, and a log out link.

In terms of work flow, I created a new branch and exclusively coded all of my front-end features there.

### JSON responses into JS model objects and prototype methods

In my newly created restaurants.js file, I wrote a Restaurant() constructor function that would take a restaurant instance and turn it into a JavaScript model object.

My project has two prototype methods for Restaurants—formatIndex() and formatShow()—and one prototype method for Comments: revealComments(). I used template literals in all three methods, and the functions all return the HTML that will get added to the DOM. 

### Rendering an index page

For this requirement, I hijacked the “Browse Restaurants” link in the navigation bar using fetch. In the Restaurants controller, I updated the index action to allow JSON rendering. The formatIndex() function is called on the list of restaurants that are returned in the fetch request. 

When a user is signed in, his/her homepage has a “View my recommended restaurants” link. When clicked, a fetch request will be made and that user’s recommended restaurants will appear. The DOM is updated with the HTML from the formatIndex() model object function.

### Rendering a show page and displaying the has-many relationship

To satisfy this requirement, I have my restaurant show pages loading via JavaScript. When a user clicks on a restaurant from the restaurants index page, the app will load that restaurant’s show page via a fetch request. The show page presents the user with the restaurant name, cuisine, and city, as well as a clickable list of chefs who recommend the restaurant. 

In my app, a chef has many comments. At the bottom of each restaurant show page is a “See Recommendations” link. When clicked, the link “disappears” and a list of chefs’ comments is revealed on the same page. This is done by a fetch request in my comments.js file.

### Rendering a form and submitting dynamically

After a user adds a new restaurant, he/she will need to add a comment about that restaurant. The new comment form is a nested resource under the restaurant that was added (i.e., /restaurants/:id/comments/new). 

When the comment form is submitted, the inputs are serialized via the serializeArray() function and saved in a variable called ‘data.’ Using AJAX, the new comment is appended to the div with ID “comment_list.”

### Other things to note and final thoughts

When I first coded the restaurant show page, I was seeing stray commas between each restaurant’s comment. After some Googling, I learned that by default, ES6 template literals use the toString() method when the .map() method is called, which automatically adds commas between each item in the newly returned array. Calling .join(‘’) at the end of the template literal removed the commas. 

```
Restaurant.prototype.formatShow = function() {
  let restaurantHtml = `
    <h1>${this.name}</h1>
    <p><strong>Cuisine:</strong> ${this.cuisine}</p>
    <p><strong>City:</strong> ${this.city.name}</p>
    <p><strong>Recommended by:</strong></p>
    <ul>
      ${this.chefs.map((chef) => `
        <li>
        <a href="/chefs/${chef.id}" class="chef_links">${chef.name}</a>
        </li>
        `
      ).join('')
    }
    </ul>

    <a href="/restaurants/${this.id}/comments" id="see-recs">See Recommendations</a>
    `
  return restaurantHtml
}
```

There are a few things I want to update. For example, my app’s URLs currently get messed up when the “back” or “forward” buttons are clicked in the browser. I also want to add the cuisine search functionality back in to this version of the app. Finally, I know it's not aesthetically pleasing, but I worried that fiddling around with CSS would take up too much time. It's definitely on my list of to-dos to strengthen my portfolio.

Working on this project helped me become a lot more comfortable with JavaScript. I also feel my debugging skills have improved. I'm excited for React and Redux, and I can't believe I'm almost done with the course!


