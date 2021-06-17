---
layout: post
title:      "Use React Hooks for Fetch request."
date:       2021-06-17 04:24:51 +0000
permalink:  use_react_hooks_for_fetch_request
---


Let's check out how to send a fetch request using React Hooks.

Ok, so we know all about lifecycle methods from ReactJS when using a class component. The lifecycle events that a React component goes through, from initially loading to ending(initialization, mounting, updating, and unmounting). Well, pretty commonly if we want to send a fetch request, we would do so with our componentDidMount lifecycle. This one seems like the best fit since it will send the fetch request only if our component 'DidMount', making everything else render before we send our fetch request.

Sending a fetch request within a class component with a lifecycle method would look something like this:

```
import React, { Component } from 'react';

export default class NewComponent extends Component {
    constructor(){
        super()

        this.state = {
            info: []
        }
    }

    componentDidMount() {
        fetch('https://yourapi.example.com')
        .then( response => response.json() )
        .then( json => this.setState({ info: json.data }) )
    }

    render() {
        return (
            <div>
                <h1>Some Data</h1>
            </div>
        )
    }
    
}
```

Now, this seems easy enough while using a class, but maybe you are choosing to use a functional component. Then this is where React Hooks come in, instead of having to worry about if our `componentDidMount`, `componentDidUpdate`, or `componentWillUnmount`, we can simply use a  Hook like `useEffect`.

Let's try understanding what `useEffect` is. Just like `componentDidMount` tells your component to run at a specific time, `useEffect` tells your component to do the same thing, but also every time there is an update in your component and at the very first time your component renders. So you can see why this one Hook can be very useful instead of needing to reference all your mounts and updates.

Hopefully, you got a bit of an understanding. The docs are always there if you need them. Now what I really brought you here for is to show you how to send that same fetch request but with hooks.

Ok, so how does this work. Start by importing our Hook from React, if we do not import it then we can not use the particular hooks you are wanting to use. In this case, I will only need to import `useEffect` since that is all I am going to use for now.

```
import React, { useEffect } from 'react';

```

We set up our component as a function:

```
export default function NewComponent() {


    return (
        <div>
            <h1>Some Data</h1>
        </div>
    )
}

```

Ok now we set up `useEffect` in our component (tell our component to run something after a render) :

```
import React, { useEffect } from 'react';

export default function NewComponent() {

   useEffect( () => {
        fetch('https://yourapi.example.com')
        .then( response => response.json() )
        .then( json => console.log(json.data))
    }, [])

    return (
        <div>
            <h1>Some Data</h1>
        </div>
    )
}

```


You will notice after our callback function, the fetch request inside `useEffect`, we are setting an empty array. This is allowing `useEffect` to only run once. If you need it to run after every single render then you can leave the empty array out, and no second argument. You can read more on that in the React Docs. So really what's happening is we are telling our component "hey do (this effect) this fetch request after rendering."

Well really, this is it. I mean we are barely scratching the surface. This is a pretty simple way of using React Hooks to send a fetch request. I want to mention that I am doing a console log with the data since I am not changing state but you can easily set that up with a Hook.

Similarly using a Hook. `useState()` can help us set up local state, but let's make sure we import it first:

```
import React, { useState, useEffect } from 'react';


const [ data, setData] = this.useState({})

//data and setData can be called anything ( `const [ stuff, setStuff] = this.useState({})` ), this is just what I went with for the example

```

This is how we set up our local state. It will essentially initialize a local state of an empty object called `data`.  Whatever you pass into `useState()` as an argument is what will be set as your state, it can be anything you want from a string, number, or object. Setting it up like so also allows us to update state with `setData` and access our current state with `data`.

So, ill leave you with this as my final piece of information. Finally, can imagine easily updating your state within your fetch request:

```
import React, { useState, useEffect } from 'react';

export default function NewComponent() {

   const [ data, setData ] = this.useState({})
	 
	 
   useEffect( () => {
        fetch('https://yourapi.example.com')
        .then( response => response.json() )
        .then(  json => setData(json) )
    }, [])

    return (
        <div>
            <h1>Some Data</h1>
						<p>{ data }</p>
        </div>
    )
}

```

And that's it! As far as the fetch and changing our state. Of course, there is much more to do but you got it from here now. I hope it was a good way for you to get started and or if you were just stopping by to read thank you. Be sure to check out the docs for ***MUCH MORE INFO*** on hooks, effect, and state.

