---
layout: post
title:      "Places I've Been: My Final Flatiron Project"
date:       2018-12-03 14:43:00 +0000
permalink:  places_ive_been_my_final_flatiron_project
---


So here we are, the final Flatiron project!

I actually spent my first few days on this project working on an app called Weekend Away, but I soon discovered that my ideas for it were too grand. I scaled it back and decided to pursue another travel app idea called Places I’ve Been.

I like to think of it as part Instagram/part travel journal. Since moving to Dublin in October 2017, my husband and I have had the opportunity to travel all over Europe. Even before then, we were always avid travelers. To remember all the “places I’ve been,” I created this app that would let me create a visual list of attractions. I’m able to upload the name, city, year visited, and a photo. I can also add notes about a particular place so that I can remember certain aspects about it or jot down the way I felt when I first saw it. 

## New Concepts

Getting started was more difficult with this project because of the whole back-end/front-end connection. I admittedly had some trouble wrapping my head around all of it. However, reading the article linked in the project readme, as well as watching this Learn video, definitely helped me set everything up.

React-router was also something I had forgotten about. It was taught at the end of the React section, but reading the official documentation and [this blog post](https://medium.com/@pshrmn/a-simple-react-router-v4-tutorial-7f23ff27adf) helped me figure out how to set up my routes, particularly my Attraction component. I also found Tyler McGinnis's videos helpful ([here's](https://tylermcginnis.com/react-router-programmatically-navigate/) a great one demonstrating the Redirect component, which I ended up implementing in conjunction with my create and delete attraction actions). There's so much I still need to learn about React-Router, and I'm looking forward to digging into this more in the future.


## The Back-end
I created an Attraction model with properties of name, city, year_visited, img_url, and notes. Initially, I had a separate City model that had a has_many association to Attractions, but I found myself running into a bit of trouble when creating the Attraction form in Redux. I ultimately decided for this project to just have the user type in the city name, but I'm hoping to flesh this out more in the future and integrate this feature. I definitely want to do some more research into the redux-form library. 

The actions I have in the AttractionController are create, index, show, and destroy. 

## Routes 
My app has three main routes in its navigation bar: Home, My Places, and Add a Place. I can also view an individual place and destroy it.

The home, or root, page shows a visual representation of my places. I implemented a flip-card feature so that when I hover over a card, it "flips" and shows the name of the place. This page is purely presentational to show a nice grid of photos.

My Places, which is located at /places, is essentially an index of attraction cards. Each card shows the name, city, and year visited of a place, as well as a photo. Each card also has a "see more' link, which will take me to the place's individual show page (/places/:id). This show page goes on to show my thoughts of the place. There's also a delete button, which will delete the attraction and redirect me to My Places. 

To add a place I've been to, I complete the form at /places/new. Once the form is submitted, it will take me to the places index page.

## Containers
I ended up with more containers than originally thought. As I started adding more functionality, I realized I needed to provide these components with state. In my code, I divided them up into folders describing which functionality the component related to. I ended up having folders for "attraction" (code related to the individual attraction show page); attractionForm (self-explanatory there); and "attractions" (code related to the index functionality, so anything having to do with the Home and My Places pages).

## Components
I have five presentational components: the navigation bar, the attraction photocard (the flipping photos that appear on the Home page), the attraction card (as seen at /places), the attraction photo (seen on the place's individual show page), and the attraction info (rendered on the individual show page).

## Process 
As I was coding and incorporating Redux, I found it helped to write out the various steps that I should expect to occur with each action. Here's what I wrote when I was working on the "My Places" functionality: 

When “My Places” is clicked, an assortment of cards appear. Each card is rendered by the AttractionCard.js presentational component. This component only has the JSX required to render an attraction’s information.

AttractionCard.js is rendered by its parent container, AttractionList.js. This container is where the bulk of the work is made in hitting the back-end API.

First, when the AttractionList.js component mounts, we call this.props.getAttractions(). This is calling our imported action, getAttractions(). The code for getAttractions() lives in actions/attractions.js.

getAttractions() is a function that returns a dispatch, which returns a fetch request to our back-end API that has all of our attractions (${API_URL}/attractions). The fetch request response is turned into JSON. This JSON will be all of our attractions. We then take these attractions and make them the argument that’s passed in to our setAttractions function, which simply returns a JavaScript object that has a type and the attraction information. That JavaScript object is then dispatched to our store to update the state. 

## A Major Issue

I came across a snag in my code when I was trying to do a fetch request to my backend for a specific attraction. 

Here was my action code before:

```
export const getAttraction = () => {
  return dispatch => {
    return fetch(`${API_URL}/attractions/${??}`)
      .then(response => response.json())
      .then(attraction => dispatch(setAttraction(attraction)))
      .catch(error => console.log(error));
  }
}
```

I was unsure how to grab the attraction ID from the AttractionCard. I was able to get the appropriate ID in my AttractionContainer component using this.props.match.params.id ('match' is an available prop from React-Router), but how to get that id value to my action? 

I started trying all sorts of solutions, including putting the fetch request directly into AttractionContainer’s componentDidMount function. It was hitting the correct URL and retrieving the right information, but I noticed in my Redux DevTools that my getAttraction reducer wasn’t getting hit (and therefore not updating my attraction state). I literally spent hours debugging this bit of code. 

Finally, I took a step back. Earlier, I had tried hardcoding the fetch URL (`${API_URL}/attractions/1`) and I saw that everything worked. My problem was literally getting the attraction’s id into my getAttraction function. 

Well, how about just passing the const id in to getAttraction? 

```
class AttractionContainer extends Component {
  componentDidMount() {
    const id = parseInt(this.props.match.params.id)
    this.props.getAttraction(id)
  }

//in actions/attractions.js
export const getAttraction = (id) => {
  return dispatch => {
    return fetch(`${API_URL}/attractions/${id}`)
      .then(response => response.json())
      .then(attraction => dispatch(setAttraction(attraction)))
      .catch(error => console.log(error));
  }
}	
```

Voila, it worked.

So simple, yet I REALLY overthought it. 

## Final Thoughts
I can't believe I've reached the end of this course! To think back to January when I started and barely knew how to code ... and now, five projects and hundreds of labs and lessons later. I feel happy with my final project and I hope to keep building upon it and adding new features as I continue to expand my knowledge about React and Redux. 
