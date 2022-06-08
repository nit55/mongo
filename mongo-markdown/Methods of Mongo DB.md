# Methods of Mongo DB

To list all DB's

```js
show dbs
```

To list all collections in a db switch to the required db using use and then show collections

```js
use dbName -> use flights;
show collections;
```

- To create a new DB

```js
use flights;
```

- this just creates a DB but is not saved until a collection and a subsequent document is entered, if not it is dropped.

```js
db.passengers.insertOne({ name: "nitin" });
```

- once inserted the db and collection are saved along with the documents inside.
- To drop a collection use

```js
db.collectionName.drop();
```
