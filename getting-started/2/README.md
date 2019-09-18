# UID's, updates, traversals and deletes 

Welcome to the second tutorial. 
I'm Karthic Rao, a Developer Advocate at Dgraph labs. 
In the last video, we learned the basics of Dgraph, including how to run the database, add new nodes and predicates, and query them back.

---

![Graph](./images/graph.JPG)

In this tutorial, we'll build the above graph and learn the following operations using it, 

- [Querying using uids.](#query-using-uids)
- [Updating predicates.](#updating-predicates) 
- [Adding an edge between existing nodes.](#adding-an-edge-between-existing-nodes)
- [Traversing the edges.](#traversing-the-edges) 
  - [One level traversals](#one-level-traversals) 
  - [Two level traversals](#two-level-traversals)
  - [Recursive traversals](#recursive-traversals)
- [Deleting a node.](#deleting-a-node)

Ensure that Dgraph and Ratel are running. Refer to the last video for instructions to run Dgraph.

Let's first create the graph using a simple mutation, 

Go to Ratel, and run mutation to create the graph.

```sh
{
  "set":[
    {
      "name": "Michael",
      "age": 40,
      "follows": {
        "name": "Pawan",
        "age": 28,
        "follows":{
          "name": "Karthic",
          "age": 28
        }
      }
    }
  ]
}
```
 
![mutation-1](./images/add-data.gif)

---

## Query using uids

The inbuilt function `uid()` returns nodes with the matching uids.   

Let's see it in action. 

Copy the UID of the node with `name` predicate set to `Michael`. 

In the query below, replace the uid `0x1388` with the `uid` you just copied. 

Let's run the query, 

```sh
{
    find_using_uid(func: uid(0x13881)){
        name 
        age
    }
}
```


![get_node_from_uid](./images/query-uid.png)

---

## Updating predicates

You can update the predicates of a node using `uid`. 

Let's update the `name` of `Michael` to `Michael Compton`.  

Here's the mutation, let's go to the mutate tab and execute,  
```sh
{
  "set":[
    {
      "uid": "0x13881",
      "name": "Michael Compton"
    }
  ]
}
```

The mutation above updates the `name` predicate of the node with matching uid to `Michael Compton`.
In case the predicate doesn't already exist, the mutation creates a new one. 

Let's validate the update using a query, 

```sh
{
    find_using_uid(func: uid(0x13881)){
        name 
        age
    }
}
```

![update check](./images/update-check.png)

The update is successful.

---

## Adding an edge between existing nodes

Similarly, you can add an edge between already existing nodes.

Consider the scenario where `Karthic` starts to follow `Michael`.
We could use the `uids` of both these nodes to create a `follows` edge between them. 
First, let's copy the `uids` from `Ratel.` 
The `uid` of `Karthic` is `0x13883`. 
We already have the uid of `Michael.` 

Let's execute the mutation, 

```sh
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


Now, with `Karthic` following `Michael,` the new Graph would look like this, 

<Add an image> 
 
---

## Traversing the edges

You can also traverse the edges connecting the nodes with queries.
Traversals take advantage of the relationship between the node. 
It helps uncover interesting facts about your data.

Let's revisit the graph we've built, <need to update the graph below>
![Graph](./images/graph.JPG). 
Traversals help you answer questions like, 
- Whom does Michael follow? 
- Whom does Pawan follow? 

What's more interesting is that the traversals can be extended to multiple levels. 

A two-level traversal could answer questions like, `Give me all the followers of people whom Michael follow.` 

Deeper traversals help unearth complex relationships and patterns in the data.

---

#### One level traversals 

Let's find how to find the people who `Michael` follow using one level traversal query. 

```sh
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

Let's dissect the query,
- The `uid()` function returns nodes with the matching uids.
  This returns the root nodes. 
- Using the name of edge  (`follows`) triggers graph   traversals from all the root level nodes. 
  This traverses and retuens all the nodes which are connected to the root level nodes via `follows` edge.
  These nodes are level 1 nodes. 

![Traversal](./images/traversal-explain.JPG)

In our simple graph, Michael follows only one person. 
Hence the traversal only returns one node.

---

#### Two-level traversals 

You cam traverse further from level 1 nodes to level 2 nodes by extending the query. 
Let's try that next.

The first level of traversal returns people, followed by Michael. 
The next level of traversal further returns the people they follow. 


```sh
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

---

#### Recursive traversals

Remember using the query for Level 2 traversal? Let's further expand it to traverse to another level, 


```sh
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

Though this works, it's not a great way to traverse the graph.
The queries become harder to compose as you traverse deeper.
The `Recurse()` directive is ideal for multiple level of traversals.

Recurse queries let you traverse a subset of the graph.
Either until we reach all leaf nodes or we reach the maximum depth which is specified by the depth parameter.

```sh
{
  find_follower(func: uid(0x13881))@recurse(depth: 4, loop:true) {
    name 
    age
    follows
  }
}
```

![recurse](./images/recurse.png)

We achieve the same result, but with a much easier querying experience. 

[Check out the docs](https://docs.dgraph.io/query-language/#recurse-query) for detailed instructions on using the `recurse` directive.

---

## Deleting a node

Ratel makes it easier to compose `delete` mutations using the `uid's.` 

Just click on the node and click on the delete button. 
This auto-fills the mutation; let 's run it. 

![delete](./images/delete.gif)

---

## Wrapping up

In this tutorial, we learned the CRUD operations using UID's and `recurse()` function.  

Before we wrap up here's a sneak peek into our next tutorial. 

Did you know that you could do text search on predicates? 

Sounds interesting? 

See you all soon in the next tutorial, till then, happy Graphing!

---




