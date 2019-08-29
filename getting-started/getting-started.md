### Getting started with Dgraph

Welcome to getting started with Dgraph. I'm Karthic Rao,  Developer Advocate at Dgraph labs. In this tutorial, we'll learn to build a simple graph on Dgraph, 

![The simple graph](./images/gs-1.png)

In the process we'll learn about, 

- Running Dgraph using Docker compose. 
- Run the following basic operations using Dgraph's UI Ratel, 
  - Create node
  - Create an edge between the nodes.
  - Query for the nodes.
   
---

## Running Dgraph
Docker-compose is the quickest way to get started with Dgraph. Ensure that 
Docker and docker-compose installed on your machine. You can find the docker-compose 
configuration in the documentation site at docs.dgraph.io. 
Go to the `Getting Started` section on the left, scroll down and you can to 
find the Docker compose configuration. 

Alternatively, you could also find the docker-compose configuration from 
[this link](https://github.com/dgraph-io/dgraph/blob/master/wiki/content/get-started/index.md#docker-compose)  below.

Copy the configuration, paste it in a file by name `docker-compose.yml`.
 Now, it's just a matter of running `docker-compose up`, and you have 
 Dgraph up and running. 

---

### Nodes and Edges
In this exercise, let's build a simple graph with two-node and an edge connecting them.

![The simple graph](./images/gs-1.png)
In a Graph database, any real-world objects or entities are represented as nodes.
May it is a sale, a transaction, a place or a person, all these entities are 
represented as a node in a Graph database.

An edge represents the relationship between the nodes. The two nodes in the above graph represent 
people, `Karthic` and `Gary`. You could also see that these nodes have two 
associated properties, `name` and `age`. These properties of the nodes are 
also called as `predicates` in Dgraph. 

Karthic and Gary are friends. The `friend` edge between them represents their 
relationship.  The edge connecting two nodes are also called as `uid predicates` in Dgraph. 

Like I mentioned earlier, we'll be using Ratel, Dgraph UI, for performing the 
necessary operations. The docker-compose setup should already have Ratel up and 
running. Just visit `http://localhost:8000` from your browser, and you should be able to access Ratel. 


![ratel-1](./images/gs-2.png)

We'll be using the latest stable release of Ratel. 

![ratel-2](./images/gs-3.png)

---

### Mutations using Ratel
The Create, Update and Delete operations in Dgraph are called as mutations. 

Using Ratel, you could run queries and obtain their graph visualizations, 
perform mutations, update the schema and much more. 

Let's go the Mutate tab and create the nodes using the set operation. Let's 
write the mutation first, but wait! It's not complete yet! 


```json
{
  "set": [
    {
      "name": "Karthic",
      "age": 28
    },
    {
      "name": "Gary",
      "age": 31

    }
  ]
}
```

The set function takes an array of JSON values and creates a node for each of them. 
The query above creates two nodes, one corresponding to each of the JSON value. 
However, it doesn't create an edge between these nodes. 

A small modification to the query above would create an edge between these two nodes.   

```json
{
  "set":[
    {
      "name": "Karthic",
      "age": 28,
      "friends": {
        "name": "Gary",
        "age": 28
      }
    }
  ]
}
```

![explain mutation](./images/explain-1.JPG)

Let's execute this mutation. Click Run and boom! 
![Query-gif](./images/mutation-1.gif)

---

### Querying using has function

Now, let us run a query to obtain the nodes which we just created. 
We'll be using Dgraph's `has` function in our query. 
In short, `has(my_predicate)` returns one or more nodes with `my_predicate` in it. 
We know that both nodes created have predicate name and age in them.

```sh
{
  people(func: has(name)) {
    name 
    age
  }
}
```

Go to the `Query` tab and type in the query.
Click `Run` on the top right of the screen. 

![query-1](./images/query-1.gif)

Two nodes appear in the result. On clicking the nodes, you could observe that 
the nodes are assigned uid's. You could even use these uid's to query for nodes. 
We'll be doing that in the next video. 


Alternatively, you could also view the JSON results from the JSON tab on the right. 


![query-2](./images/query-2.gif)

---

### Deciphering the query.

![Illustration with explanation](./images/explain-2.JPG)
 
The first part of the query is the user-defined function name. In this case, 
we have named it as `people`. However, one could use any name.  

`func` keyword has to be assigned to an inbuilt function. Dgraph offers a variety 
of inbuilt functions. The `has`  function is one of them.  Check out the 
[query language guide](https://docs.dgraph.io/query-language) to know more about 
other in-built functions in Dgraph. Even better, just subcribe to the channel and follow along :)

The inner fields are similar to select statement in SQL. `Has` selects the nodes, 
the inner field defines the list of predicates to be present for each node in the included in the result. 

Similarly, we could use the `has` function to find all nodes with the `age` 
predicate. You could also alter the inner fields. 

```json
{
  people(func: has(age)) {
    name
  }
}
```

---

### More on mutations
We can extrapolate the mutation syntax to create more nodes and edges. 


```json
{
  "set":[
    {
      "name": "Michael",
      "age": 40,
      "friends": {
        "name": "Pawan",
        "age": 28,
        "friends":{
          "name": "Natalya",
          "age": 30
        }
      }
    }
  ]
}
```

---

### Flexible schema 
Dgraph implicitly doesn't enforce a structure or a schema to the JSON 
properties/predicates of a node. It's flexible. 
Here is one such example, 

```json
{
  "set":[
    {
      "name": "Karthic",
      "age": 28,
      "country" : "India"
    },
    {
      "name": "Gary",
      "age": 28,
      "city": "Sydney"
    }
  ]
}
```

We are creating two nodes, but the first node has predicates `name, age and country`, 
the second one has `name, age and city`. 

In the upcoming tutorials, we'll see how the newly introduced type system 
feature allows us to associate types and follow a strict schema approach for nodes. 

---

### Wrapping up
That's it for this video. I hope you all had fun. In the next video, let's 
learn about writing queries and mutations based on the uid's of the node. These 
mutations include the update and delete operations. See you all soon, till then, happy Graphing! 

---

