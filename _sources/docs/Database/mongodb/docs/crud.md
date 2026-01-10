# CRUD Operations

MongoDb is stores data in BSON format.
BSON is Binary JSON which extends JSON to include additional data types.
BSON Example document:

```json
{
  "_id": ObjectId("694d4f16848798841e25940e"), # ObjectId is a unique identifier for a document.
  "pName": "Inspiron 5230",
  "make": "Dell",
  "makeYear": 2025
}
```

MongoDB CRUD operations are as follows:

## Create

```bash
# Syntax
db.collectionName.insertOne(<data>, <options>);

# insert one document
db.products.insertOne({...});

# insert multiple documents
db.products.insertMany([{...},{...},{...}]);

```

### Examples

```bash
# insert one document
db.products.insertOne({"pName": "Inspiron 5230", "make":"Dell","makeYear":2025});

# insert multiple documents
db.products.insertMany([
    {"pName": "Inspiron 5230", "make":"Dell","makeYear":2025},
    {"pName": "Inspiron 6230", "make":"Dell","makeYear":2026},
    {"pName": "Inspiron 7230", "make":"Dell","makeYear":2027}
]);
```

## Read

```bash
db.collectionName.find(<query>, <options>);
db.collectionName.findOne(<query>, <options>);
db.collectionName.findMany(<query>, <options>);
```

### Examples

```bash
# find count of documents
db.products.count();

# find one document
db.products.findOne({"pName": "Inspiron 5230"});

# find multiple documents
db.products.findMany({"makeYear": 2025});

# find all documents
db.products.find({});

# find all documents with specific fields
db.products.find({}, {"pName": 1, "makeYear": 1});

# find all documents with specific fields and sort
db.products.find({}, {"pName": 1, "makeYear": 1}).sort({"makeYear": 1});

# find all documents with specific fields and limit
db.products.find({}, {"pName": 1, "makeYear": 1}).limit(10);

# find all documents with specific fields and skip
db.products.find({}, {"pName": 1, "makeYear": 1}).skip(10);

# find all documents with specific fields and sort and limit
db.products.find({}, {"pName": 1, "makeYear": 1}).sort({"makeYear": 1}).limit(10);

# find all documents with specific fields and sort and skip
db.products.find({}, {"pName": 1, "makeYear": 1}).sort({"makeYear": 1}).skip(10);

# find all documents with makeYear greater than 2025
db.products.find({"makeYear": {"$gt": 2025}});

# find all documents with makeYear greater than 2025 and less than 2027
db.products.find({"makeYear": {"$gt": 2025, "$lt": 2027}});
```

:::{tip}
`db.products.find():` will gives cursor of all documents. Cursor is an iterator that points to a set of documents in a collection. It will give first 20 documents.
`db.products.find().toArray():` will gives array of all documents. Array is a collection of documents.
`db.products.find().forEach(<callback>)` will gives each document one by one.
:::

## Update

```bash
# update one document
db.collectionName.updateOne(<query>, <update>, <options>);
db.collectionName.updateMany(<query>, <update>, <options>);

# replace one document
db.collectionName.replaceOne(<query>, <update>, <options>);
db.collectionName.replaceMany(<query>, <update>, <options>);
```

### Examples

```bash
# update one document
db.products.updateOne({"pName": "Inspiron 5230"}, {$set: {"make": "Dell"}});

# update multiple documents
db.products.updateMany({"makeYear": 2025}, {$set: {"makeYear": 2026}});

# ❌ override document, not recommended use replaceOne instead
db.products.update({"pName": "Inspiron 5230"},
    {"pName": "Inspiron 5230", "make": "Dell", "makeYear": 2026});

# ✅ replace one document
db.products.replaceOne({"pName": "Inspiron 5230"}, {"pName": "Inspiron 5230", "make": "Dell", "makeYear": 2026});

# ✅ replace multiple documents
db.products.replaceMany({"makeYear": 2026}, {"makeYear": 2027});
```

## Delete

```bash
# delete one document
db.collectionName.deleteOne(<query>, <options>);
db.collectionName.deleteMany(<query>, <options>);
```

### Examples

```bash
# delete one document
db.products.deleteOne({"pName": "Inspiron 5230"});

# delete multiple documents
db.products.deleteMany({"makeYear": 2026});

# delete all documents
db.products.deleteMany({});
```

## Projection

Projection is used to select specific fields from a document. It is used to reduce the amount of data transferred from the database to the application.

```bash
# projection
db.products.find({}, {"pName": 1, "makeYear": 1});

# projection without id field
db.products.find({}, {"_id": 0, "pName": 1, "makeYear": 1});

```

## Embedded Documents

Embedded documents are documents that are stored within another document.

## Arrays

Arrays are used to store multiple values in a single field.
