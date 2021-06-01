---
layout: post
title:      "Render an HTML Table with React/Redux - Boostrap"
date:       2021-05-31 21:02:06 -0400
permalink:  render_an_html_table_with_react_redux_-_boostrap
---


Ok, to start we are going to assume you have a good idea of what React is and you have some bootstrap knowledge. Today I'm gonna walk you through rendering an HTML table with your react props and while using bootstrap to add a little flare. Also, I will assume you have access to some type of data, from an external API or a backend API.

We want to start by making sure we have bootstrap installed in our current React app. You can check your `package.json` for this and if you don't have it installed then run `npm install react-bootstrap`.  Once your terminal is done installing, then you are ready to go (obviously we are assuming you have your react app started, sorry I am not going to walk you through an entire app process, maybe next time).

Alright, (I know I am assuming a lot of you and I should not be prejudging you that much.. sorry.) we are going to assume you know about and are connected to the Redux store.

Let's create a container called `GameContainer`, grab our state from the redux store and we will use those props to pass down an array of objects to the `GameTable` component. That array of objects comes from `props.games` and we will pass it down as `games` so that the child component can have access to it as props. We should also pass down our array of strings to `GameTable` called `headers` that has table headers for each column. This is because I knew in advance what I want to use as my row of headers, keep in mind you won't have to pass the headers down, you can just add them to the table at the end. Ok, let us see this container:

```
const GameContainer = (props) => {

    const headers = [ 
        'Title', 
       ' Platform',
        'Genre',
        'Completed',
        'Delete',
        'Visit'
    ]

    return (
        <div>
            < GameTable headers={headers} games={props.games}/>
        </div>
    )
}

const mapStateToProps = (state) => ({
    user: state.user,
    games: state.user.games
})

export default connect(mapStateToProps)(GameContainer);
```




*****Before we continue, we need to add `import 'bootstrap/dist/css/bootstrap.min.css'`  to our main index.js file*

We now have your container built, now let's go into our `GameTable` component to render our table and rows. Here we will set up our main table with the table head and body, then we will render our headers and game data into each of those table areas individually by mapping our data. First, let's import a `Table` component from bootstrap, so at the top of the page where your imports go let's add `import Table from 'react-bootstrap/Table';` Perfect, now since we have access to the bootstrap table component we can return a table in our class. The table syntax stays similar, except for your components being imported now start with a capital letter, `Table` :


> `const games`  is mapping through our game data that was sent over as props from our parent container and for each game we are rendering a `GameRow` component to pass through each game. Then we return `{ games }` inside our table body `<tbody></tbody>`.

```

const GameTable = (props) => {

        const headers = props.headers.map( (head, i) => <th key={i} >{head}</th> ) 
				
        const games = props.games.map( (game, index ) => <GameRow key={index} game={game} /> )

        return (
            <div>
                <Table striped bordered hover>
                    <thead>
                        <tr>
                            { headers }
                        </tr>
                    </thead>
                    <tbody>
                        { games }
                    </tbody>
                </Table>
            </div>
        )


}

export default GameTable;

```

> We are doing something similar with our headers, mapping through our headers array, but here instead we are displaying it to our page. Notice `{ headers }` is inside a table row tag `<tr></tr>` , which is inside of table head tag `<thead></thead>`.


You can head over to the bootstrap doc at https://react-bootstrap.github.io/ and read through the docs. Check all the different styles you can change within your Table component. For now we are sticking pretty safely with `<Table striped bordered hover`, but feel free to explore the docs and change up the styles or table sizes.


Ok, almost there! Since we are mapping through each game and passing its data to `GameRow`, we need to create a component called `GameRow`. I have been using functional components so we will stick with that. Remember we are returning each game within the `<tbody></tbody>` tag so to render a row from each game we have to have our component return a row:

```

const GameRow = (props) => {

    const game = props.game
    
    return (
        <tr>
        </tr>
    )

}

export default GameRow;

```

And each row will contain data cells from each of the game's data. The syntax for a normal data cell in a row is `<td></td>`. What we will do here, is grab our props being passed down and render them in our return. Placing each attribute of a game in a `<td></td>` tag, like so:

> Remember that we mapped through each game in our parent component, and are passing down each game(object) to `GameRow`.

```

const game = props.game
    
    return (
        <tr>
            <td>{game.title}</td>
            <td>{game.platform}</td>
            <td>{game.kind}</td>
        </tr>
    )
		
```

Finally, let's see the final component `GameRow` with all our code pieced together.

```
const GameRow = (props) => {

    const game = props.game
    
    return (
        <tr>
            <td>{game.title}</td>
            <td>{game.platform}</td>
            <td>{game.kind}</td>
        </tr>
    )

}

export default GameRow;
```

We did it, I think.. uh hmmm. I mean, I am no senior dev but I believe we did just fine! The bootstrap part is pretty easy. You would think there would be more involved from reading my title, building with bootstrap, but the thing is bootstrap makes it all so easy and effortless. Just by importing `import Table from 'react-bootstrap/Table';` our table component `<Table></Table>`, bootstrap styles our app. It's amazing! I hope this was easy enough to follow, we kept it a bit basic to understand the main concept of passing through your state and using props in your children's components. Overall hope this helps someone and you enjoyed the read.
