# Cursor Object

- The find() method by default returns only 20 documents and a cursor Object
- Cursor Object consists of metaData that allows us to cycle through the result.
- The it command returns the next 20 results and the cursor Object updated and so forth we can cycle through the entire collection.
- If we need all the results in one shot we need to export it as an array as shown below.

```js
db.passengers.find().toArray();
```

- we can also loop through for a more realistic/performance based approach where cursor goes through each document using forEach.

```js
db.passengers.find().forEach((passenger) => {
  printJson(passenger);
});
```

- pretty() is also a method attached to the cursor, it works on find() but not on findOne() as findOne() does not return a cursor Object.
