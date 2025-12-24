# Mongo DB

## Installation

1. Download .msi file from https://www.mongodb.com/try/download/community
2. Install MongoDB as a Service on windows by double clicking on .msi file.
3. Goto MongoDB installation directory and click on mongo.exe/mongosh.exe to launch MongoDB Shell.
4. Start MongoDB service on windows.

```bash
net start MongoDB
```

```bash
net stop MongoDB
```

5. Use MongoDB shell to connect to MongoDB from command line.

```bash
mongosh
```

## Commands to interact with MongoDB from MongoDB Shell

1. Show all Databases in MongoDB Server.

```bash
show dbs
```

```shell
# select db
use admin
```

2. create revsdb, products collection and insert data into collection.

```bash
show dbs

use revsdb

db.products.insertOne({"pName": "Inspiron 5230", "make":"Dell","makeYear":2025})

# Output
{
  acknowledged: true,
  insertedId: ObjectId('69315ab9d8726b39781e2621')
}
```

2. Read data from products collection.

```bash
db.products.find().pretty()
```

## Resetting Your Database

Important: We will regularly start with a clean database server (i.e. all data was purged) in this course.

To get rid of your data, you can simply load the database you want to get rid of (use databaseName) and then execute db.dropDatabase().

Similarly, you could get rid of a single collection in a database via db.myCollection.drop().
