---
layout: post
title:      "Fetch Request with a JS Class"
date:       2021-03-22 02:08:37 -0400
permalink:  fetch_request_with_a_js_class
---


For our JavaScript project, we had to use a backend API with rails. We had to somehow grab the data from the API with JavaScript. Luckily for us, our instructors showed us how to do just that. With Fetch API.

A method you can call on a JavaScript object to return an HTTP response that can be extracted into JSON, with a method. We created our projects with Object-Oriented JavaScript (classes), so why don't we start by setting up our class. Pretty simple, nothing too crazy since we won't need to be passing in any info through our constructor.

```
class FetchGameApi{
  constructor(){}
	
 };
```

Now, let's create a static method within that class that is accessible in other files. We can access static methods by calling our class and invoking that static method on it.

**We know we want to make the fetch request with this method so remember naming conventions.*

```
class FetchGameApi{
  constructor(){}
	
	static getGamesFetch(){};
	
 };
```

Alright, we have our class, our method and now let's declare method. We need to return `fetch()` and as an argument pass in a URL. This is letting our request know where it needs to go. To extract the JSON data we chain the `then()` method to `fetch()` and return `.json()` with an anonymous function. In this case, we are performing a `GET` request for the JSON data so not much is required, this should be just enough to get your information.

```
static getGamesFetch(){
    return fetch("http://localhost:3000/games")
    .then( response => response.json() )
  };
```

**However, if it is not working you can also chain a `catch()` method to catch any error messages and display them onto your console.*

```
static getGamesFetch(){
    return fetch("http://localhost:3000/games")
    .then( response => response.json() )
    .catch( error => console.log(error.message) )
  };
```

Alright, success! Now, that was a bit simpler since we only needed to grab the data. Let's go ahead and send a `POST` request with`fetch()`. We can add this to our existing class. This should be fairly similar except we need to clarify a few things in a second parameter in `fetch()`. Our second parameter will be an Object consisting of key-value pairs of the method type, the type of data, and of course the data itself(which is also an object of key-value pairs, that will be converted to JSON). 

> In this case, we will assume you have data of some sort (like from a form) that is ready to send through our `fetch() post` request. 

**1st `method`: our object needs to specify which `fetch()` method, `POST` in this case.*

```
{
      method: POST,
     }
```

**2nd headers: we need to add headers for which type of data is being handled.*

```
{
      method: 'POST',
			headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
        }
      }
```

**3rd `body`: our data, which should be an object with key-value pairs that will be stringified to JSON.*

```
{
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
      body: JSON.stringify({
			   name: name,
				 info: info,
				 stuff: stuff
		  })
    }
```


Ok let's throw that into a function, remember it's a static method. Since we called our last static method `getGamesFetch()`, lets call this one `postGamesFetch()`. I know, pretty good huh. Also, let's add a parameter to our static method since we will need to pass in data somehow.

```
class FetchGameApi{
  constructor(){
  }


  static getGamesFetch(){
    return fetch("http://localhost:3000/games")
    .then( response => response.json() )
    .catch( error => console.log(error.message) )
  };


  static postGamesFetch(dataObject){
    fetch("http://localhost:3000/games", {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
       body: JSON.stringify(dataObject)
    .then( resp => resp.json())
  };
```
	
	
Ok looking good but we still need to chain `then()` to `fetch()`. Remember you could also chain `catch()` after `then()` to catch errors. Alrighty then, we are all set. Now we just need to invoke our static methods where needed.

**static methods have to be invoked on its class.*


> FetchGameApi.getGamesFetch()

> FetchGameApi.postGamesFetch(dataObject)


Voila, we are fetching! Sounds kinda like fishing. Pretty cool. Anyways, this should help you get the job done if needed. I hope you could follow along.  If not, there is plenty of great documentation out there for more help. Goodbye and take care. Ok, you know what, I will show you my function where I am getting my form data from but that's it!

**We have an event listener on a form submit, that will pass in the form data to our static method once submitted.*

```
function addNewGameFromForm( form ){
  form.addEventListener( 'submit', (e) => {
e.preventDefault()
    let formData = new FormData( e.target )
    const gameData = {
      title: formData.get( 'title' ),
      image: formData.get( 'image' ),
      review: formData.get( 'review' )
    }
    FetchGameApi.postGamesFetch( gameData )
  })
};
```

Ok, I hope that gives you a better idea of what type of data we would be sending. Thanks for reading.
