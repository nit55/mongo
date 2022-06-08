# Delete Operations in MongoDB

## deleteOne(filter,options)

- A criteria/Filter is required for delete operations.
- We are deleting the first document we encounter in the collection.
- If filter is not applied gives an error as it is mandatory.

```js
db.passengers.deleteOne({ name: "Nitin" });
```

```js
{ "acknowledged" : true, "deletedCount" : 1 }
```

## deleteMany(filter,options)

- If no arguments are passed then all documents are deleted else the filtered items are only deleted

```js
db.passengers.deleteMany({});
```
