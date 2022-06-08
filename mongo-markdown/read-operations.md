# Read Operations in Mongo DB

## find(filter,options)

- find(filter,options) method returns the results in the db upto 20 elements.

pretty() makes sure the data is displayed in a formatted manner

find() without filter returns all results, pointing upto only 20 results.

```js
db.collectionName.find().pretty();
```

- Output of above snippet

```js
{
        "_id" : ObjectId("629e42fbca7bf6a925e2"),
        "name" : "Max Schwarzmueller",
        "age" : 29
}
{
        "_id" : ObjectId("629e42fbca7bf6a925e2"),
        "name" : "Manu Lorenz",
        "age" : 30
}
```

- find() with filters
- finds all the passengers with age greater than 38.

```js
db.passengerData.find({ age: { $gt: 38 } }).pretty();
```

## findOne(filter,options)

- as the name suggests used to find the first occurence of any particular document based on a filter/criteria.

```js
db.passengerData.findOne({ age: { $gt: 38 } });
```
