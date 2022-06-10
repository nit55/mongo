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

---

## title: Update documents

## db.collection.updateOne

#### [Docs](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne)

### Syntax

```javascript
db.collection.updateOne(
   <filter>,
   <update>,
   options
)
```

### Update with any field

//case sensitive

```javascript
try {
  db.products.updateOne(
    {
      item: "box",
    },
    {
      $set: {
        qty: 99,
      },
    }
  );
} catch (e) {
  print(e);
}
```

```javascript
{
    "acknowledged": true,
    "matchedCount": 1,
    "modifiedCount": 1
}
```

### Update with upsert option

(upsert = inserts if not found)

```javascript
try {
  db.products.updateOne(
    {
      item: "Pizza",
    },
    {
      $set: {
        _id: 14,
        item: "Pizza",
        violations: 7,
      },
    },
    {
      upsert: true,
    }
  );
} catch (e) {
  print(e);
}
```

```javascript
{
   "acknowledged" : true,
   "matchedCount" : 0,
   "modifiedCount" : 0,
   "upsertedId" : 4
}
```

### Update array

With arrayFilters option

Consider following data

```javascript
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      grades: [95, 92, 90],
    },
  }
);
db.products.updateOne(
  {
    _id: 11,
  },
  {
    $set: {
      grades: [98, 100, 102],
    },
  }
);
db.products.updateOne(
  {
    _id: 12,
  },
  {
    $set: {
      grades: [95, 110, 100],
    },
  }
);
db.products.updateOne(
  {
    _id: 12,
  },
  {
    $set: {
      gradesSecondary: [195, 210, 250],
    },
  }
);
```

```javascript
//set grades > 100 to 100
db.products.updateOne(
  {
    grades: {
      $exists: true,
    },
    _id: {
      $gt: 10,
    },
  },
  {
    $set: {
      "grades.$[elementA]": 100,
    },
  },
  {
    arrayFilters: [
      {
        elementA: {
          $gte: 100,
        },
      },
    ],
  }
);
```

```javascript
//set grades > 100 to 100 & gradesSecondary > 200 to 200 (if both exists)
db.products.updateOne(
  {
    grades: {
      $exists: true,
    },
    gradesSecondary: {
      $exists: true,
    },
    _id: {
      $gt: 10,
    },
  },
  {
    $set: {
      "grades.$[elementA]": 100,
      "gradesSecondary.$[elementB]": 200,
    },
  },
  {
    arrayFilters: [
      {
        elementA: {
          $gte: 100,
        },
      },
      {
        elementB: {
          $gte: 200,
        },
      },
    ],
  }
);
```

### Update nested / deeper array

With arrayFilters option

Consider following data

```javascript
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      seasonQuarterPrices: [
        [90, 85, 85],
        [80, 85, 84],
        [83, 86, 82],
        [79, 75, 70],
      ],
    },
  }
);
db.products.updateOne(
  {
    _id: 11,
  },
  {
    $set: {
      seasonQuarterPrices: [
        [190, 185, 185],
        [180, 185, 184],
        [183, 186, 182],
        [179, 175, 170],
      ],
    },
  }
);
db.products.updateOne(
  {
    _id: 12,
  },
  {
    $set: {
      seasonQuarterPrices: [
        [290, 285, 285],
        [280, 285, 284],
        [283, 286, 282],
        [279, 275, 270],
      ],
    },
  }
);
```

```javascript
//set seasonQuarterPrices inner values >= 85 to 90
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      "seasonQuarterPrices.$[].$[a]": 90,
    },
  },
  {
    arrayFilters: [
      {
        a: {
          $gte: 85,
        },
      },
    ],
  }
);
```

### Update object array

With arrayFilters option

Consider following data

```javascript
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      gradesObjArr: [
        {
          grade: 80,
          mean: 75,
          std: 6,
        },
        {
          grade: 85,
          mean: 90,
          std: 4,
        },
        {
          grade: 85,
          mean: 85,
          std: 6,
        },
      ],
    },
  }
);
db.products.updateOne(
  {
    _id: 11,
  },
  {
    $set: {
      gradesObjArr: [
        {
          grade: 90,
          mean: 75,
          std: 6,
        },
        {
          grade: 87,
          mean: 90,
          std: 3,
        },
        {
          grade: 85,
          mean: 85,
          std: 4,
        },
      ],
    },
  }
);
```

```javascript
//in array for grade > 85 update mean
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      "gradesObjArr.$[elem].mean": 100,
    },
  },
  {
    arrayFilters: [
      {
        "elem.grade": {
          $gte: 85,
        },
      },
    ],
  }
);
```

### Update nested / deeper object array

With arrayFilters option

Consider following data

```javascript
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      "gradesObjArr.$[].quarterPrices": [
        {
          1: 90,
          2: 80,
          3: 85,
        },
        {
          1: 80,
          2: 85,
          3: 84,
        },
        {
          1: 83,
          2: 86,
          3: 82,
        },
        {
          1: 79,
          2: 75,
          3: 70,
        },
      ],
    },
  }
);
```

```javascript
//set (some grade) gradesObjArr->quarterPrices inner 1st month values >= 80 to 90
db.products.updateOne(
  {
    _id: 10,
  },
  {
    $set: {
      "gradesObjArr.$[a].quarterPrices.$[b].1": 90,
    },
  },
  {
    arrayFilters: [
      {
        "a.std": 6,
      },
      {
        "b.1": {
          $gte: 80,
        },
      },
    ],
  }
);
```

---

## db.collection.updateMany

#### [Docs](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/)

### UpdateMany with any field

```javascript
try {
  db.products.updateMany(
    {
      qty: {
        $gt: 50,
      },
    },
    {
      $set: {
        ReviewRequired: true,
      },
    }
  );
} catch (e) {
  print(e);
}
```

```javascript
{
    "acknowledged": true,
    "matchedCount": 2,
    "modifiedCount": 2
}
```

### UpdateMany with upsert option

(upsert = inserts if not found)

```javascript
try {
  db.products.updateMany(
    {
      item: "paneer",
    },
    {
      $set: {
        item: "paneer",
        qty: 100,
        ReviewRequired: true,
      },
    },
    {
      upsert: true,
    }
  );
} catch (e) {
  print(e);
}
```

```javascript
{
   "acknowledged" : true,
   "matchedCount" : 0,
   "modifiedCount" : 0,
   "upsertedId" : ObjectId("56fc5dcb39ee682bdc609b02")
}
```

similarly updateMany array & object array with arrayFilters option

---

- More about positional filter ([Docs](https://docs.mongodb.com/manual/reference/operator/update/positional-filtered/))
- there is also replaceOne ([Docs](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne)) method
