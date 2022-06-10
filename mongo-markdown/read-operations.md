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

---

## title: Query documents

### [All operators](https://docs.mongodb.com/manual/reference/operator/query/)

## db.collection.find

- find returns cursor, findOne returns single document

#### [Docs](https://docs.mongodb.com/manual/tutorial/query-documents/)

### Syntax

```javascript
db.collection.find({
  <field1>: <value1>,
  <field2>: { <operator>: <value> },
  ...
})
```

### Equals

```javascript
db.products.find({
  item: "box",
});
```

### In

```javascript
db.products.find({
  item: {
    $in: ["box", "small box", "large box"],
  },
});
```

### AND

```javascript
//$nin, $gte, $lte, $eq, $ne
db.products.find({
  item: {
    $in: ["box", "small box", "large box"],
  },
  qty: {
    $gt: 20,
    $lt: 90,
  },
});
```

- $and exists

### OR

```javascript
//qty>20 and (item="box" or ReviewRequired=true)
db.products.find({
  $or: [
    {
      item: "box",
    },
    {
      ReviewRequired: true,
    },
  ],
  qty: {
    $gt: 20,
  },
});
```

- (.) $nor also exists (inverse of OR)
  - or = A || B || C
  - nor = !(A || B || C) = !A && !B && !C
- (.) $not also exists (useful to inverse any query)

### Like

```javascript
//item like 'c%'
db.products.find({
  item: /^c/,
});
```

[By Regex Keyword](https://docs.mongodb.com/manual/reference/operator/query/regex/#op._S_regex)

- so use regex pattern for case-insensitive matches
- this may hit performance (when we search between..etc) instead use text indices

### Object

Consider following

```javascript
db.products.insertMany([
  { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
  {
    item: "notebook",
    qty: 50,
    size: { h: 8.5, w: 11, uom: "in" },
    status: "A",
  },
  { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
  {
    item: "planner",
    qty: 75,
    size: { h: 22.85, w: 30, uom: "cm" },
    status: "D",
  },
  {
    item: "postcard",
    qty: 45,
    size: { h: 10, w: 15.25, uom: "cm" },
    status: "A",
  },
]);
```

```javascript
//exact size == object (also prop order must be matched) (so dont use this)
db.products.find({
  size: {
    uom: "cm",
  },
});
```

```javascript
db.products.find({
  "size.uom": "cm",
  "size.h": {
    $gt: 10,
  },
});
```

### Array

```javascript
//exact match
db.products.find({
  grades: [95, 92],
});
```

```javascript
// all match (array must contain both these values at minimum)
db.products.find({
  grades: {
    $all: [95, 92],
  },
});
```

```javascript
//Query an Array by Array Length
db.products.find({
  grades: {
    $size: 3,
  },
});
```

```javascript
/*(dont use this) any element in grades > 96 
(If you specify only a single <query> condition in the $elemMatch expression,
you do not need to use $elemMatch.)*/
db.products.find({
  grades: {
    $gt: 96,
  },
});
```

```javascript
//any element in grades > 96
db.products.find({
  grades: {
    $elemMatch: {
      $gt: 96,
    },
  },
});
```

```javascript
//(not working ) or condition for $elemMatch ??
db.products.find({
  grades: {
    $or: [
      {
        $elemMatch: {
          $gt: 96,
        },
      },
      {
        $elemMatch: {
          $eq: 90,
        },
      },
    ],
  },
});

//instead using this alternative
db.products.find({
  $or: [
    {
      grades: {
        $elemMatch: {
          $gt: 96,
        },
      },
    },
    {
      grades: {
        $elemMatch: {
          $eq: 90,
        },
      },
    },
  ],
});
```

```javascript
db.products.find({
  grades: {
    $elemMatch: {
      $in: [95, 92],
    },
  },
});
```

```javascript
//Query for an Element by the Array Index Position
//second element > 96
db.products.find({
  "grades.1": {
    $gt: 96,
  },
});
```

### Object Array

```javascript
db.products.find({
  //prefer
  gradesObjArr: {
    $elemMatch: {
      mean: {
        $gt: 99,
      },
    },
  },
});
```

```javascript
//Query for an object Element by the Array Index Position
//wrong. for object use dot notation else equality issue
db.products.find({
  "gradesObjArr.1": {
    mean: {
      $gt: 99,
    },
  },
});
//right
db.products.find({
  "gradesObjArr.1.mean": {
    $gt: 99,
  },
});
```

- **We can use same syntax like object for object array**, but

  - Consider Example :

  ```javascript
  db.products.find({
    "gradesObjArr.mean": {
      $gt: 99,
    },
  });
  ```

  - issue with above syntax is, when we have multi condition then all wont apply on same array element.

  ```javascript
  db.products.find({
    //test it
    "gradesObjArr.mean": {
      $gt: 50,
    },
    "gradesObjArr.avg": {
      $gt: 100,
    },
  });

  let doc = {
    name: "test",
    gradesObjArr: [
      {
        mean: 60, //> 50
        avg: 60,
      },
      {
        mean: 10,
        avg: 110, //> 100 , so current document is selected
      },
    ],
  };
  ```

### Nested Object Array

```javascript
//consider follwing
db.products.insertOne({
  item: "Nested Card",
  qty: 15,
  regionsObjArr: [
    {
      name: "bangalore",
      price: 200,
      alias: ["toofan card", "badshah card", "tashan card"],
    },
    {
      name: "chennai",
      price: 180,
      alias: ["rajini card", "top card"],
    },
    {
      name: "calcutta",
      price: 170,
    },
  ],
});
```

```javascript
db.products.find({
  regionsObjArr: {
    $elemMatch: {
      price: {
        $gt: 170,
      },
      alias: {
        $elemMatch: {
          $in: ["toofan card", "so card"],
        },
      },
    },
  },
});
```

### Null + exists

```javascript
//consider follwing
db.products.insertOne({
  item: "SuperCard",
  qty: 15,
  grades: null,
});
```

```javascript
//either grades is null or grades property doesnot exist
db.products.find({
  grades: null,
});
```

```javascript
//to get only  grades value as  null  ($type: 10, 10 is null value in BSON type)
db.products.find({
  grades: {
    $type: 10,
  },
});
```

[type](https://docs.mongodb.com/manual/reference/operator/query/type/#op._S_type) has number value, also name alias

```javascript
//to check property exist or not
db.products.find({
  grades: {
    $exists: true,
  },
});
```

- $exists + grades: null check
- $exists + grades -> $ne : null check

### $expr

- [docs](https://docs.mongodb.com/manual/reference/operator/query/expr/#op._S_expr)
- useful to compare fields from the same document
- check **$cond** example in above docs link

```javascript
db.monthlyBudget.find({
  $expr: {
    $gt: ["$spent", "$budget"],
  },
});
```

- $expr with other operators

```javascript
db.monthlyBudget.find({
  $expr: {
    $gt: [
      "$createdDate",
      {
        $toDate: "2020-02-14T05:51:44.264Z",
      },
    ],
  },
});
```

- To compare dates

```javascript
db.monthlyBudget.find({
  $or: [
    {
      $expr: {
        $gt: ["$spent", "$budget"],
      },
    },
    {
      $expr: {
        $lt: ["$spent", 30],
      },
    },
  ],
});
```

<hr />

## Projection

```javascript
//only specific fields (here 2) (can remove the _id field by 0)
db.products.find(
  {},
  {
    grades: 1,
    item: 1,
  }
);
```

```javascript
//all other fields except these
db.products.find(
  {},
  {
    grades: 0,
    item: 0,
    "size.h": 0,
  }
);
```

```javascript
//object
db.products.find(
  {},
  {
    "size.h": 1,
    item: 1,
  }
);
```

```javascript
//object array  (so array/ object same projection style)
db.products.find(
  {},
  {
    "gradesObjArr.mean": 1,
  }
);
```

#### Note: to give alias name (.)

```javascript
{
 sizeHeight:'$size.h',
 item : 1
}
```

### elemMatch in projection

**(don't use - prefer aggregation pipeline)**

[docs](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch)

- say in array, we want to project some matching 1st element

- **$elemMatch projection returns only the first matching element from the array.**

consider

```javascript
{
 _id: 1,
 zipcode: "63109",
 students: [
              { name: "john", school: 102, age: 10 },
              { name: "jess", school: 102, age: 11 },
              { name: "jeff", school: 108, age: 15 }
           ]
}
{
 _id: 2,
 zipcode: "63110",
 students: [
              { name: "ajax", school: 100, age: 7 },
              { name: "achilles", school: 100, age: 8 },
           ]
}
{
 _id: 3,
 zipcode: "63109",
 students: [
              { name: "ajax", school: 100, age: 7 },
              { name: "achilles", school: 100, age: 8 },
           ]
}
{
 _id: 4,
 zipcode: "63109",
 students: [
              { name: "barney", school: 102, age: 7 },
              { name: "ruth", school: 102, age: 16 },
           ]
}
```

```javascript
db.schools.find(
  { zipcode: "63109" },
  { students: { $elemMatch: { school: 102 } } }
);
```

```javascript
{ "_id" : 1, "students" : [ { "name" : "john", "school" : 102, "age" : 10 } ] }
 //2 matched, but only one returned for _id = 1
{ "_id" : 3 }
{ "_id" : 4, "students" : [ { "name" : "barney", "school" : 102, "age" : 7 } ] }
```

### $slice in projection

[docs](https://docs.mongodb.com/manual/reference/operator/projection/slice/#proj._S_slice)

- controls the number of items of an array that a query returns

- first five items in an array in the comments field

```javascript
db.posts.find({}, { comments: { $slice: 5 } });
```

- the last five items in array

```javascript
db.posts.find({}, { comments: { $slice: -5 } });
```

- will only return 10 items, after skipping the first 20 items of that array.

```javascript
db.posts.find({}, { comments: { $slice: [20, 10] } }); //skip, return
```

- returns 10 items as well, beginning with the item that is 20th from the last item of the array.

```javascript
db.posts.find({}, { comments: { $slice: [-20, 10] } });
```

<hr />

## db.collection.sort

#### [Docs](https://docs.mongodb.com/manual/reference/method/cursor.sort/)

### Syntax

```javascript
db.collection.sort({
  <field1>: <value1>
  ...
})
```

### Examples

```javascript
db.collection.sort({
  item: 1,
});
```

```javascript
//for descending order
db.collection.sort({
  item: -1,
});
```

```javascript
//To sort case insensitively(English Alphabets)
db.collection.sort({
 item:1
})le Objects use insertMany(), it accepts an array.
db.passengers.insertMany([
  { name: "prasan", age: 30 },
  { name: "Lakshmi", age: 29 },
]);

.collation({'locale':'en'})  // Add collation
```

---

- Find one [(Docs)](https://docs.mongodb.com/manual/reference/method/db.collection.findOne)

- Aggregation pipeline, the map-reduce function, and single purpose aggregation methods

- .find().count() method exists

  Note : After find(), we do .toArray(), forEach() loop to execute cursor in application.

- [More on Cursors](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/)
- Better write queries in js files (formatted) & execute that file in shell
