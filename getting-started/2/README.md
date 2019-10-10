# UIDs, updates, traversals and deletes 
Hi, welcome to the second episode of getting started with Dgraph. 
In the last video, we learned some of the basics of Dgraph. 
Including, how to run the database, add new nodes and predicates, and query them back.

<Karthic will be replaced by Mary in the Graph below, it's just a reference for now>
![Graph](./images/graph.JPG)

In this tutorial, we'll build the above Graph and learn more about operations using the UID of the nodes.
Specifically, we'll learn about:
- Querying, updating, and deleting nodes using their UIDs.
- Adding an edge between existing nodes.
- Traversing the Graph.

First, let's create our Graph.

Go to Ratel's mutate tab, paste the mutation below in the text area, click Run.


```json
{
  "set":[
    {
      "name": "Michael",
      "age": 40,
      "follows": {
        "name": "Pawan",
        "age": 28,
        "follows":{
          "name": "Mary",
          "age": 28
        }
      }
    }
  ]
}
```


<Will replace the Gif below with a screenshot> 
![mutation-1](./images/add-data.gif)


## Query using UIDs
The `UID` of the nodes can be used to query them back.
The built-in function `uid()` takes either a `UID` of a node or a list of `UIDs` as an input argument.
And, it returns nodes with the matching UIDs on execution.

Let's see it in action. 
First, let's copy the UID of the node created for `Michael`. 

Go to the query tab, type in the query below, and click Run.
```graphql
{
  people(func: has(name)) {
    name
    age
  }
}
```

Now, from the result, copy the `UID` of Michael's node. 

In the query below, replace the UID `0x1388`, with the `UID` you just copied.
Dgraph generates these UIDs based on some complex logic.
Hence, we might have a small chance of having the same UIDs.

Let's run the following query.

```graphql
{
    find_using_uid(func: uid(0x13881)){
        name 
        age
    }
}
```

![get_node_from_uid](./images/query-uid.png)

You can see that the `uid()` function returns the node matching the UID `0x13881`.

Refer to the last episode if you have questions related to the structure of the query in general.

## Updating predicates
You can also update one or more predicates of a node using its `UID`.

Michael recently celebrated his 41st birthday. Let's update his age to 41. 

Let's go to the mutate tab and execute the mutation.
Again, don't forget to replace the `UID` with the one you just copied. 


```json
{
  "set":[
    {
      "UID": "0x13881",
      "age": "41"
    }
  ]
}
```

We had earlier used `set` to create new nodes. 
Although, on using the `UID` of an existing node, it updates its predicates, instead of creating a new node.

You can see that Michael's age is updated to 41.

```graphql
{
    find_using_uid(func: uid(0x13881)){
        name 
        age
    }
}
```

![update check](./images/update-check.png)

Similarly, you can also add new predicates to an existing node.
Since the predicate `country` doesn't exist for the node with the UID `0x13881`, it creates a new one.

```json
{
  "set":[
    {
      "uid": "0x13881",
      "country": "Australia"
    }
  ]
}
```

## Adding an edge between existing nodes
You can also add an edge between existing nodes using their UIDs.

Let's say, `Mary` starts to follow `Michael`.

We know that this relationship between them has to represented by creating the `follows` edge between them.

<Add the illustration of the new Graph>

First, let's copy the `UIDs` of nodes for `Mary` and `Michael` from Ratel.

Now, replace the UIDs with the ones you copied, and execute the mutation.

<An illustration will explain which one Michael's UID and which one is Mary's in the mutation below>


```json
{
  "set":[
    {
      "uid": "0x13883",
      "follows": {
        "uid": "0x13881"
      }
    }
  ]
}

```

![Edge between nodes](./images/edge-between-nodes.gif)
 
## Traversing the edges
Graph databases offer many distinct capabilities. `Traversals` are among them.

Traversals answer questions or queries related to the relationship between the nodes.
Hence, queries like, `Whom does Michael follow?`
are answered by traversing the `follows` relationship.

Let's run a traversal query and then understand it in detail.

```graphql
{
    find_follower(func: uid(0x13881)){
        name 
        age
        follows {
          name 
          age 
        }
    }
}
```

Here's the result. 

![traversal-result](./images/traversal.png)

<Add an illustration to explain the query>


The query has three parts:
1. **Selecting the root nodes.**
First, you need to select one or more nodes as the starting point for traversals. 
These are called the root nodes.
In the query above, we use the `uid()` function to select the node created for `Michael` as the root node.
<br />

2. **Choose the edge to be traversed**
You need to specify the edge to be traversed starting from the selected root nodes.
And then, the traversal, travels along these edges, from one end to the nodes at the other end.

In our query, we chose to traverse the `follows` edge starting from the node for `Michael`.
The traversal returns all the nodes connected to the node for `Michael` via the `follows` edge.
<br />

3. **Specify the predicates to get back**
Since Michael follows only one person, the traversal returns just one node.
These are `level-1` nodes. 
Again, we need to specify which predicates you want to get back from level-1 nodes.

You can extend the query to make use of `level-1` nodes and traverse the Graph deeper.
Let's explore that in the next section.

#### Multi-level traversals 
The first level of traversal returns people, followed by Michael. 
The next level of traversal further returns the people they in-turn follow. 

This pattern can be repeated multiple times to achieve multi-level deep traversals. 
That's when we say that the query is deep! 


<funny illustration with Diggy saying that's so deep>


```graphql
{
  find_follower(func: uid(0x13881)) {
    name 
    age 
    follows {
      name
      age
      follows {
        name 
        age
      }
    }
  }
}
```

![level-2-query](./images/level-2-query.png)

```graphql
{
  find_follower(func: uid(0x13881)) {
    name 
    age 
    follows {
      name
      age
      follows {
        name 
        age
        follows {
          name 
          age
        }
      }
    }
  }
}
```

![level 3](./images/level-3.png)

This query is really long!
If you ask, isn't there are an in-built function that makes multi-level deep queries or traversals easy?

The answer is Yes!
That's what the `recurse()` function does.
Let's explore that in our next section.

#### Recursive traversals
Recurse queries makes it easier to perform multi-level deep traversals.
They let you easily traverse a subset of the Graph.

With the following recursive query, we achieve the same result as our last query.
But, with a much better querying experience. 

```graphql
{
  find_follower(func: uid(0x13881))@recurse(depth: 4, loop:true) {
    name 
    age
    follows
  }
}
```

![recurse](./images/recurse.png)

[Check out the docs](https://docs.dgraph.io/query-language/#recurse-query) for detailed instructions on using the `recurse` directive.

#### Edges have directions
Edges are in Dgraph are directional.

For instance, the `follows` edge emerging from the node for `Michael`, points at the node for `Pawan`.
They have a notion of direction.

Traversing along the direction of the edge is natural to Dgraph.
But, you need to add a database index to able to traverse the edge in the reverse direction.

We'll explore more about indexes in Dgraph in our future episodes.

## Deleting a node
Ratel also makes it easier to compose mutation to delete a node.
Just click on the node and then click the delete button. 
In the text area, you should now see a mutation to delete the node.

Let's run it.

![delete](./images/delete.gif)


## Wrapping up
In this tutorial, we learned about the CRUD operations using UIDs.
We also learned about `recurse()` function.

Before we wrap, here's a sneak peek into our next tutorial.

Did you know that you could search predicates based on their value?

Sounds interesting?

See you all soon in the next tutorial, till then, happy Graphing!