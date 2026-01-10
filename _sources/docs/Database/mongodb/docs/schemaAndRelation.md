# Data Types, Schema and Relations

MongoDb is a NoSQL database that stores data in a document format. Each document is a collection of key-value pairs. The key is a string and the value can be a string, number, array, object, etc.

## Data Types

1. String: Ex: "Revs"
2. Number: Ex: 21
   - Integer: Ex: 21
   - Integer64: Ex: 4154154541
   - Float: Ex: 21.21
3. Array: Ex: ["Revs", "Tech", "Docs"]
4. Object: Ex: {"name": "Revs", "age": 21}
5. Boolean: Ex: true/false
6. ISODate: Ex: `new Date()`, 2025-12-25T22:56:59.000Z
7. Timestamp: Ex: `new Timestamp()`, 1756262219
8. ObjectID: Ex: 676d6d6d6d6d6d6d6d6d6d6d
9. Null: Ex: null
10. Embedded Object: Ex: {"name": "Revs", "age": 21}
11. Embedded Array: Ex: ["Revs", "Tech", "Docs"]

## Schema

Schema is a blueprint for a document. It defines the structure of the document and the data types of the fields.

MongoDb does not have a fixed schema. This means that you can store different types of data in the same collection.

## Relations

Relations are used to define the relationship between two documents. There are three types of relations:

1. One to One
2. One to Many
3. Many to Many
