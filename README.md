# MongoDB + Node.js 


## Quick Start

This guide will show you how to set up a simple application using Node.js and MongoDB. Its scope is only how to set up the driver and perform the simple CRUD operations. For more in-depth coverage, see the [official documentation](https://www.mongodb.com/docs/drivers/node/).

### Create the `package.json` file

First, create a directory where your application will live.

```bash
mkdir myProject
cd myProject
```

Enter the following command and answer the questions to create the initial structure for your new project:

```bash
npm init -y
```

Next, install the driver as a dependency.

```bash
npm install mongodb
```

### Start a MongoDB Server

For complete MongoDB installation instructions, see [the manual](https://www.mongodb.com/docs/manual/installation/).

1. Download the right MongoDB version from [MongoDB](https://www.mongodb.org/downloads)
2. Create a database directory (in this case under **/data**).
3. Install and start a `mongod` process.

```bash
mongod --dbpath=/data
```

You should see the **mongod** process start up and print some status information.

### Connect to MongoDB

Create a new **app.js** file and add the following code to try out some basic CRUD
operations using the MongoDB driver.

Add code to connect to the server and the database **myProject**:



```js
const { MongoClient } = require('mongodb');
// or as an es module:
// import { MongoClient } from 'mongodb'

// Connection URL
const url = 'mongodb://localhost:27017';
const client = new MongoClient(url);

// Database Name
const dbName = 'myProject';

async function main() {
  // Use connect method to connect to the server
  await client.connect();
  console.log('Connected successfully to server');
  const db = client.db(dbName);
  const collection = db.collection('documents');

  // the following code examples can be pasted here...

  return 'done.';
}

main()
  .then(console.log)
  .catch(console.error)
  .finally(() => client.close());
```

Run your app from the command line with:

```bash
node app.js
```

The application should print **Connected successfully to server** to the console.

### Insert a Document

Add to **app.js** the following function which uses the **insertMany**
method to add three documents to the **documents** collection.

```js
const insertResult = await collection.insertMany([{ a: 1 }, { a: 2 }, { a: 3 }]);
console.log('Inserted documents =>', insertResult);
```

The **insertMany** command returns an object with information about the insert operations.

### Find All Documents

Add a query that returns all the documents.

```js
const findResult = await collection.find({}).toArray();
console.log('Found documents =>', findResult);
```

This query returns all the documents in the **documents** collection.
If you add this below the insertMany example you'll see the document's you've inserted.

### Find Documents with a Query Filter

Add a query filter to find only documents which meet the query criteria.

```js
const filteredDocs = await collection.find({ a: 3 }).toArray();
console.log('Found documents filtered by { a: 3 } =>', filteredDocs);
```

Only the documents which match `'a' : 3` should be returned.

### Update a document

The following operation updates a document in the **documents** collection.

```js
const updateResult = await collection.updateOne({ a: 3 }, { $set: { b: 1 } });
console.log('Updated documents =>', updateResult);
```

The method updates the first document where the field **a** is equal to **3** by adding a new field **b** to the document set to **1**. `updateResult` contains information about whether there was a matching document to update or not.

### Remove a document

Remove the document where the field **a** is equal to **3**.

```js
const deleteResult = await collection.deleteMany({ a: 3 });
console.log('Deleted documents =>', deleteResult);
```

### Index a Collection

[Indexes](https://www.mongodb.com/docs/manual/indexes/) can improve your application's
performance. The following function creates an index on the **a** field in the
**documents** collection.

```js
const indexName = await collection.createIndex({ a: 1 });
console.log('index name =', indexName);
```

For more detailed information, see the [indexing strategies page](https://www.mongodb.com/docs/manual/applications/indexes/).

## Error Handling

If you need to filter certain errors from our driver we have a helpful tree of errors described in [etc/notes/errors.md](https://github.com/mongodb/node-mongodb-native/blob/HEAD/etc/notes/errors.md).

It is our recommendation to use `instanceof` checks on errors and to avoid relying on parsing `error.message` and `error.name` strings in your code.
We guarantee `instanceof` checks will pass according to semver guidelines, but errors may be sub-classed or their messages may change at any time, even patch releases, as we see fit to increase the helpfulness of the errors.

Any new errors we add to the driver will directly extend an existing error class and no existing error will be moved to a different parent class outside of a major release.
This means `instanceof` will always be able to accurately capture the errors that our driver throws.

```typescript
const client = new MongoClient(url);
await client.connect();
const collection = client.db().collection('collection');

try {
  await collection.insertOne({ _id: 1 });
  await collection.insertOne({ _id: 1 }); // duplicate key error
} catch (error) {
  if (error instanceof MongoServerError) {
    console.log(`Error worth logging: ${error}`); // special case for some reason
  }
  throw error; // still want to crash
}
```


© Shovan Roy, MS in Computer Science Student on OPT EAD, Looking for a job 
@ sroy1401@student.sfbu.edu, Mobile: 5107502568
