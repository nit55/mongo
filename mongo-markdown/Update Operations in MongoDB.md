# Update Operations in MongoDB

- Make sure to pass a filter for update else all the records in the collection will be reset with only the data passed in the update method.

## updateOne(filter,data,options)

- Filter is Mandatory to update a Record.

```js
db.passengers.updateOne({ age: 31 }, { $set: { age: 30 } });
```

- filter like greater than/lesser than also can be used to update the data

```js
db.passengers.updateOne({ age: { $lt: 31 } }, { $set: { age: 30 } });
```

- The output would be as follows

```js
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

## updateMany(filter,data,options)

```js
db.passengerData.updateMany({ age: { $lt: 38 } }, { $set: { age: 21 } });
```

- The output is as follows

```js
{ "acknowledged" : true, "matchedCount" : 12, "modifiedCount" : 11 }
```

## replaceOne(filter,data)

- This is used to replace the entire document based on a condition

```js
db.passengers.replaceOne(
  { _id: ObjectId("629e42fbca7bf6a925e2") },
  { name: "Narayana", age: 28 }
);
```
