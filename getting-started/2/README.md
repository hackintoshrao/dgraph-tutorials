# UID's, Updates, deletes and traversals
#### Taking forward the momemtum
In the last [lesson](../1/README.md), we learnt a bunch of things.
We learnt about running Dgraph using docker-compose, mutations and couple of simple queries. 

Let's take forward the momemtum, and continue from where we left off. 
Here's is what we'll be learning in this tutorial.

- Using uid's to run queries. 
- Update the nodes. 
- Delete the nodes. 
- Traversing the edge. 

## Using the node ID's for querying
In the last tutorial, we saw that Dgraph assigns an unique identifier `uid`, to the newly created nodes. 

Let's take a closer look at these uid's and use them to run queries. 


#### Running Dgraph 
Let's quickly get Dgraph up and running and insert some sample data. 

```sh
$ wget https://raw.githubusercontent.com/hackintoshrao/dgraph-tutorials/master/getting-started/1/docker-compose.yml

$ docker-compose up
```

In case you still have the data from the last lesson, let's delete them,

Go to Schema -> bulk edit - >Drop ALL 

![drop-all](./images/drop-all.gif)


---

#### Add sample data 
Let's get started! 

Let us build the following graph and do some interesting things around it, 

![Graph](./images/graph.JPG)

In this graph, Michael `follows` Pawan, who in turn `follows` Karthic. 

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

Now we have the node, let's query for them and check out the graph we've built. 

Along with name and age, you could also add ask for `uid` in the list of predicates returned.

```sh
{
    findUID(func: has(name)) {
        uid 
        name 
        age 
    }
}
```

Click on nodes to expand them.

![click expand](./images/click-expand.gif)

We have our Graph!

---
#### Using the uid() function

So far, we've only used Dgraph's `has` function in the queries. 

The inbuilt function `uid()` enables us to use the `uid` of nodes for queries and mutations.

Let's copy the UID of node with `name` predicate set to `Michael`. 
We'll be using it to query for the node. 

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
#### Updating the node

Using the `uid` we could even update and delete a node.

Let's update the name of `Michael` to `Michael Compton`.  

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

When a `uid` of node is used in a `set` function, it updates predicates of an existing node. 

Let's test the update using a query, 

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

#### Traversing the edges
Edges connecting the nodes can be traversed in queries.
Traversals make use of relationship between the nodes to explore the data.

Let's revisit the graph we've built, 
![Graph](./images/graph.JPG). 
Traversals helps you answer questions like, 
- Who does Michael follow? 
- Who does Pawan follow? 

What's more interesting is that the traversals can be extended to multiple levels. 

A two level traversal could answer questions like, `Give me all the followers of people who Michael follow`. 

More deeper traversals help unearth complex relationships and patterns in the data.

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

- The `uid()` function is the criteria for selecting root level nodes. 
- It's followed by the selecting predicates from selected root level nodes.
- Using name of an edge triggers traversals. 
  The edge connecting nodes are also called `uid predicates` in Dgraph.
- In this case we traverse to all those nodes which are connected to the root level nodes via `follows` edge.
 These are level 1 nodes. 

![Traversal](./images/traversal-explain.JPG)

In our simple simple graph Michael follows only one person. 
Hence the traversal only return one node.

---

#### Two level traversals 
We could traverse further from level 1 nodes to level 2 nodes by extending the query. 
Let's try that next.

Level 1 traversal return people followed by Michael. 
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

#### Deleting a node


---




