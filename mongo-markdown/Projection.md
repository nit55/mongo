# Projection

- Sometimes we dont need to dispatch/display all the keys in a document, under such circumstances we can use projection.

- As the name suggests we only project/display a part of the data rather than the entire object.

- The first parameter is the filter, if empty it means all the documents in the collection.
- the second parameter (options) tells which fields to include in the output.
- By default \_id is always 1 which means it will be displayed, if not required explicitly set to 0
- For other fields it will be default 0, only the ones we set as 1 will be displayed.
- If the options parameter is not passed all the fields will be displayed.

```js
db.passengers.find({}, { name: 1, _id: 0 });
```

```js
{ "name" : "Max Schwarzmueller" }
{ "name" : "Manu Lorenz" }
{ "name" : "Chris Hayton" }
{ "name" : "Sandeep Kumar" }
{ "name" : "Maria Jones" }
{ "name" : "Alexandra Maier" }
{ "name" : "Dr. Phil Evans" }
{ "name" : "Sandra Brugge" }
{ "name" : "Elisabeth Mayr" }
{ "name" : "Frank Cube" }
{ "name" : "Karandeep Alun" }
{ "name" : "Michaela Drayer" }
{ "name" : "Bernd Hoftstadt" }
{ "name" : "Scott Tolib" }
{ "name" : "Freddy Melver" }
{ "name" : "Alexis Bohed" }
{ "name" : "Melanie Palace" }
{ "name" : "Armin Glutch" }
{ "name" : "Klaus Arber" }
{ "name" : "Gordon Black" }
```

- as seen from the above the output we have projected/displayed only the name field.
