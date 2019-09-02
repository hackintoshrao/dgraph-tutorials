### Taking forward the momemtum
In the last [lesson](../1/README.md), we learnt ahout running Dgraph using dockerc-compose, mutations and couple of simple queries. 

Let's take forward the momemtum, and in this tutorial, let us learn about more queries, udpate and delete mutation. In this tutorial, we'll learn about, 

- Using uid's to run queries. 
- Traversing the edges in queries. 
- Update the nodes. 
- Delete the nodes. 

### Using the node ID's for querying
In the last tutorial, we saw that Dgraph assigns an unique identifier `uid`, to all the newly created nodes. 

Let's take a closer look at these uid's and use them to run queries. 

You could use the same [docker compose](../1/docker-compose.yml) file from the last tutorial. 


Let's quickly get Dgraph up and running and insert some sample data, 

```sh
$ wget https://raw.githubusercontent.com/hackintoshrao/dgraph-tutorials/master/getting-started/1/docker-compose.yml

$ docker-compose up
```



