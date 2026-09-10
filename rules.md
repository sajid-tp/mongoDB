# MongoDB Commands Cheat Sheet

> MongoDB uses **Databases → Collections → Documents → Fields** rather than SQL's databases → tables → rows → columns.

---

## 1. MongoDB Structure

| MongoDB    | SQL Equivalent | Meaning                          |
| ---------- | -------------- | -------------------------------- |
| Database   | Database       | Container for collections        |
| Collection | Table          | Group of related documents       |
| Document   | Row            | One record/object                |
| Field      | Column         | A property inside a document     |
| `_id`      | Primary Key    | Unique identifier for a document |

---

## 2. Database Commands

| Command             | Purpose                  | When / Where to Use               |
| ------------------- | ------------------------ | --------------------------------- |
| `show dbs`          | Shows all databases      | Check available databases         |
| `use databaseName`  | Switches to a database   | Start working with a database     |
| `db`                | Shows current database   | Check which database you're using |
| `db.dropDatabase()` | Deletes current database | Remove an entire database         |

### Examples

```js
show dbs

use ecommercePractice

db

db.dropDatabase()
```

> ⚠️ `db.dropDatabase()` permanently deletes the current database.

---

## 3. Collection Commands

| Command                       | Purpose              | When / Where to Use                             |
| ----------------------------- | -------------------- | ----------------------------------------------- |
| `db.createCollection("name")` | Creates a collection | When you want to explicitly create a collection |
| `show collections`            | Shows collections    | Check collections inside current database       |
| `db.collection.drop()`        | Deletes a collection | Remove an entire collection                     |

### Examples

```js
db.createCollection("products")

show collections

db.products.drop()
```

### Automatic Collection Creation

You usually don't need to create a collection manually.

```js
db.products.insertOne({
  name: "iPhone"
})
```

MongoDB automatically creates `products` if it doesn't exist.

---

# 4. Create / Insert Documents

## `insertOne()`

| Command       | Purpose              | When to Use            |
| ------------- | -------------------- | ---------------------- |
| `insertOne()` | Inserts one document | Adding a single record |

```js
db.products.insertOne({
  name: "iPhone 16",
  price: 79999,
  brand: "Apple"
})
```

---

## `insertMany()`

| Command        | Purpose                    | When to Use                     |
| -------------- | -------------------------- | ------------------------------- |
| `insertMany()` | Inserts multiple documents | Adding multiple records at once |

```js
db.products.insertMany([
  {
    name: "iPhone 16",
    price: 79999
  },
  {
    name: "Samsung S25",
    price: 74999
  }
])
```

---

# 5. Read / Find Documents

## `find()`

| Command  | Purpose                        | When to Use                    |
| -------- | ------------------------------ | ------------------------------ |
| `find()` | Returns all matching documents | When you need multiple records |

```js
db.products.find()
```

Find products by seller:

```js
db.products.find({
  seller: "Rahul"
})
```

---

## `findOne()`

| Command     | Purpose                       | When to Use                   |
| ----------- | ----------------------------- | ----------------------------- |
| `findOne()` | Returns one matching document | When you only need one record |

```js
db.products.findOne({
  seller: "Rahul"
})
```

### Important

`findOne()` does **not** mean "find one user."

It means:

> Find **one document** from the collection that matches the condition.

```text
users collection
    ↓
findOne()
    ↓
one user document

products collection
    ↓
findOne()
    ↓
one product document

orders collection
    ↓
findOne()
    ↓
one order document
```

---

# 6. Basic Filtering

| Operator / Syntax  | Meaning       | Example                                |
| ------------------ | ------------- | -------------------------------------- |
| `{ field: value }` | Exact match   | `{ brand: "Nike" }`                    |
| Multiple fields    | AND condition | `{ category: "Shoes", brand: "Nike" }` |

### Exact Match

```js
db.products.find({
  brand: "Nike"
})
```

### Multiple Conditions

```js
db.products.find({
  category: "Shoes",
  price: {
    $gt: 5000
  }
})
```

Meaning:

```text
category = Shoes
AND
price > 5000
```

---

# 7. Comparison Operators

| Operator | Meaning                    | Example                                   |
| -------- | -------------------------- | ----------------------------------------- |
| `$eq`    | Equal                      | `{ price: { $eq: 5000 } }`                |
| `$ne`    | Not equal                  | `{ brand: { $ne: "Nike" } }`              |
| `$gt`    | Greater than               | `{ price: { $gt: 5000 } }`                |
| `$gte`   | Greater than or equal      | `{ price: { $gte: 5000 } }`               |
| `$lt`    | Less than                  | `{ price: { $lt: 5000 } }`                |
| `$lte`   | Less than or equal         | `{ price: { $lte: 5000 } }`               |
| `$in`    | Matches any value in array | `{ brand: { $in: ["Nike", "Adidas"] } }`  |
| `$nin`   | Doesn't match values       | `{ brand: { $nin: ["Nike", "Adidas"] } }` |

### `$gt`

```js
db.products.find({
  price: {
    $gt: 10000
  }
})
```

### `$gte`

```js
db.products.find({
  rating: {
    $gte: 4.5
  }
})
```

### `$lt`

```js
db.products.find({
  stock: {
    $lt: 10
  }
})
```

### `$lte`

```js
db.products.find({
  price: {
    $lte: 5000
  }
})
```

---

# 8. `$in` and `$nin`

## `$in`

Find Nike OR Adidas:

```js
db.products.find({
  brand: {
    $in: ["Nike", "Adidas"]
  }
})
```

## `$nin`

Find products that are NOT Nike or Adidas:

```js
db.products.find({
  brand: {
    $nin: ["Nike", "Adidas"]
  }
})
```

---

# 9. Logical Operators

| Operator | Meaning                               | When to Use                   |
| -------- | ------------------------------------- | ----------------------------- |
| `$and`   | All conditions must be true           | Multiple conditions           |
| `$or`    | At least one condition must be true   | Alternative conditions        |
| `$nor`   | None of the conditions should be true | Excluding multiple conditions |
| `$not`   | Negates a condition                   | Reverse a condition           |

### `$or`

```js
db.products.find({
  $or: [
    { brand: "Nike" },
    { brand: "Adidas" }
  ]
})
```

Meaning:

```text
Nike OR Adidas
```

### `$and`

```js
db.products.find({
  $and: [
    { category: "Shoes" },
    { price: { $gt: 5000 } }
  ]
})
```

> Usually you don't need `$and` explicitly because multiple fields already work as AND.

---

# 10. Projection

Projection controls **which fields are returned**.

| Value | Meaning       |
| ----- | ------------- |
| `1`   | Include field |
| `0`   | Exclude field |

### Include fields

```js
db.products.find(
  {},
  {
    name: 1,
    price: 1
  }
)
```

### Hide `_id`

```js
db.products.find(
  {},
  {
    name: 1,
    price: 1,
    _id: 0
  }
)
```

### Purpose

Useful when:

* You only need specific fields
* You want smaller API responses
* You don't want to expose unnecessary data

---

# 11. Sorting

```js
db.products.find().sort({
  price: 1
})
```

| Value | Meaning    |
| ----- | ---------- |
| `1`   | Ascending  |
| `-1`  | Descending |

### Cheapest first

```js
db.products.find().sort({
  price: 1
})
```

### Most expensive first

```js
db.products.find().sort({
  price: -1
})
```

### Highest rating first

```js
db.products.find().sort({
  rating: -1
})
```

---

# 12. Limit

```js
db.products.find().limit(5)
```

| Purpose                    | Example Use            |
| -------------------------- | ---------------------- |
| Restrict number of results | Top 5 products         |
| Homepage                   | Show 10 products       |
| API response control       | Limit returned records |

---

# 13. Skip

```js
db.products.find().skip(10)
```

### Purpose

Skip a number of documents.

Mostly useful for **pagination**.

```js
db.products.find()
  .skip(10)
  .limit(10)
```

Meaning:

```text
Skip first 10
↓
Return next 10
```

---

# 14. Pagination

### Formula

```js
const skip = (page - 1) * limit
```

| Page | Limit | Skip |
| ---: | ----: | ---: |
|    1 |    10 |    0 |
|    2 |    10 |   10 |
|    3 |    10 |   20 |
|    4 |    10 |   30 |

### MongoDB

```js
db.products.find()
  .skip(skip)
  .limit(limit)
```

### Common API Usage

```js
const page = 2
const limit = 10

const skip = (page - 1) * limit

const products = await Product.find()
  .skip(skip)
  .limit(limit)
```

---

# 15. Count Documents

## `countDocuments()`

| Command            | Purpose                               |
| ------------------ | ------------------------------------- |
| `countDocuments()` | Counts documents matching a condition |

### Count all

```js
db.products.countDocuments()
```

### Count Electronics

```js
db.products.countDocuments({
  category: "Electronics"
})
```

### Common API Usage

```text
totalProducts
      ↓
totalPages
```

---

# 16. Update Documents

## `updateOne()`

| Command       | Purpose                       |
| ------------- | ----------------------------- |
| `updateOne()` | Updates one matching document |

```js
db.products.updateOne(
  {
    name: "iPhone 16"
  },
  {
    $set: {
      price: 75000
    }
  }
)
```

---

## `updateMany()`

| Command        | Purpose                        |
| -------------- | ------------------------------ |
| `updateMany()` | Updates all matching documents |

```js
db.products.updateMany(
  {
    brand: "Nike"
  },
  {
    $set: {
      isActive: false
    }
  }
)
```

---

# 17. Update Operators

| Operator  | Purpose                  | Example                            |
| --------- | ------------------------ | ---------------------------------- |
| `$set`    | Set/change a field       | `$set: { price: 5000 }`            |
| `$inc`    | Increase/decrease number | `$inc: { stock: -1 }`              |
| `$unset`  | Remove a field           | `$unset: { seller: "" }`           |
| `$rename` | Rename a field           | `$rename: { name: "productName" }` |

---

# 18. `$set`

Change or create a field.

```js
db.products.updateOne(
  {
    name: "iPhone 16"
  },
  {
    $set: {
      price: 75000
    }
  }
)
```

---

# 19. `$inc`

Increase or decrease a number.

### Increase

```js
db.products.updateOne(
  {
    name: "iPhone 16"
  },
  {
    $inc: {
      stock: 5
    }
  }
)
```

### Decrease

```js
db.products.updateOne(
  {
    name: "iPhone 16"
  },
  {
    $inc: {
      stock: -1
    }
  }
)
```

### Common e-commerce use

```text
Customer buys product
        ↓
stock decreases by 1
        ↓
$inc: { stock: -1 }
```

---

# 20. `$unset`

Removes a field.

```js
db.products.updateOne(
  {
    name: "iPhone 16"
  },
  {
    $unset: {
      seller: ""
    }
  }
)
```

---

# 21. `$rename`

Renames a field.

```js
db.products.updateMany(
  {},
  {
    $rename: {
      name: "productName"
    }
  }
)
```

---

# 22. Array Operators

| Operator    | Purpose                                   |
| ----------- | ----------------------------------------- |
| `$push`     | Add item to array                         |
| `$pull`     | Remove item from array                    |
| `$addToSet` | Add item only if it doesn't already exist |

Example:

```js
{
  name: "Nike Shoes",
  tags: ["running", "sports"]
}
```

---

## `$push`

```js
db.products.updateOne(
  {
    name: "Nike Shoes"
  },
  {
    $push: {
      tags: "casual"
    }
  }
)
```

Result:

```js
tags: ["running", "sports", "casual"]
```

---

## `$pull`

```js
db.products.updateOne(
  {
    name: "Nike Shoes"
  },
  {
    $pull: {
      tags: "sports"
    }
  }
)
```

---

## `$addToSet`

```js
db.products.updateOne(
  {
    name: "Nike Shoes"
  },
  {
    $addToSet: {
      tags: "running"
    }
  }
)
```

If `"running"` already exists, it won't add another one.

### Difference

| Operator    | Behavior                         |
| ----------- | -------------------------------- |
| `$push`     | Always adds                      |
| `$addToSet` | Adds only if not already present |
| `$pull`     | Removes matching item            |

---

# 23. Delete Documents

## `deleteOne()`

```js
db.products.deleteOne({
  name: "iPhone 16"
})
```

> Deletes one matching document.

---

## `deleteMany()`

```js
db.products.deleteMany({
  isActive: false
})
```

> Deletes all matching documents.

### ⚠️ Dangerous

```js
db.products.deleteMany({})
```

This deletes **all documents** in the collection.

---

# 24. Regular Expressions — `$regex`

Useful for search functionality.

```js
db.products.find({
  name: {
    $regex: "iphone",
    $options: "i"
  }
})
```

`i` means case-insensitive.

It can match:

```text
iPhone
IPHONE
iphone
Iphone
```

### E-commerce search

```js
db.products.find({
  name: {
    $regex: "shoe",
    $options: "i"
  }
})
```

---

# 25. Array Queries

Find products containing `"gaming"`:

```js
db.products.find({
  tags: "gaming"
})
```

Find products containing `"gaming"` OR `"running"`:

```js
db.products.find({
  tags: {
    $in: ["gaming", "running"]
  }
})
```

---

# 26. Nested Documents

Example:

```js
{
  name: "iPhone",
  seller: {
    name: "Rahul",
    city: "Kochi"
  }
}
```

### Find Rahul

```js
db.products.find({
  "seller.name": "Rahul"
})
```

### Find sellers from Kochi

```js
db.products.find({
  "seller.city": "Kochi"
})
```

This is called **dot notation**.

---

# 27. `$exists`

Check whether a field exists.

### Field exists

```js
db.products.find({
  seller: {
    $exists: true
  }
})
```

### Field doesn't exist

```js
db.products.find({
  seller: {
    $exists: false
  }
})
```

---

# 28. `distinct()`

Find unique values.

```js
db.products.distinct("category")
```

Example result:

```js
[
  "Electronics",
  "Clothing",
  "Shoes",
  "Furniture"
]
```

### Useful for

* Category filters
* Brand filters
* Unique values

---

# 29. Aggregation

Aggregation is used to **process multiple documents and produce calculated/transformed results**.

Basic structure:

```js
db.products.aggregate([
  {
    // stage 1
  },
  {
    // stage 2
  }
])
```

Think:

```text
Documents
    ↓
$match
    ↓
$group
    ↓
$sort
    ↓
$project
    ↓
Result
```

---

# 30. `$match`

Filters documents inside an aggregation pipeline.

```js
db.products.aggregate([
  {
    $match: {
      category: "Electronics"
    }
  }
])
```

Similar to:

```js
db.products.find({
  category: "Electronics"
})
```

---

# 31. `$group`

Groups documents based on a field.

### Count products per category

```js
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      totalProducts: {
        $sum: 1
      }
    }
  }
])
```

Result conceptually:

```text
Electronics → 4
Clothing    → 3
Shoes       → 2
Furniture   → 1
```

---

# 32. Aggregation Accumulators

| Operator | Purpose            |
| -------- | ------------------ |
| `$sum`   | Add values / count |
| `$avg`   | Calculate average  |
| `$min`   | Find minimum       |
| `$max`   | Find maximum       |

### `$sum`

Count:

```js
$sum: 1
```

Add prices:

```js
$sum: "$price"
```

### `$avg`

```js
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      averagePrice: {
        $avg: "$price"
      }
    }
  }
])
```

### `$min`

```js
$min: "$price"
```

### `$max`

```js
$max: "$price"
```

---

# 33. `$sort` in Aggregation

```js
db.products.aggregate([
  {
    $sort: {
      price: -1
    }
  }
])
```

---

# 34. `$limit` in Aggregation

Find top 5 most expensive:

```js
db.products.aggregate([
  {
    $sort: {
      price: -1
    }
  },
  {
    $limit: 5
  }
])
```

---

# 35. `$project` in Aggregation

Controls the output fields.

```js
db.products.aggregate([
  {
    $project: {
      name: 1,
      price: 1,
      _id: 0
    }
  }
])
```

It can also create calculated fields.

---

# 36. `$multiply`

Used for multiplication in aggregation.

Example:

```text
inventory value = price × stock
```

```js
db.products.aggregate([
  {
    $project: {
      name: 1,
      inventoryValue: {
        $multiply: ["$price", "$stock"]
      }
    }
  }
])
```

---

# 37. `$lookup`

Used to combine related data from another collection.

Similar conceptually to a SQL `JOIN`.

Example:

```text
orders
   │
   │ userId
   ↓
users
```

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user"
    }
  }
])
```

### Common use cases

* Orders → Users
* Orders → Products
* Products → Categories
* Reviews → Users
* Reviews → Products

---

# 38. `$unwind`

Turns an array into individual documents.

If `$lookup` produces:

```js
user: [
  {
    name: "Rahul"
  }
]
```

Use:

```js
{
  $unwind: "$user"
}
```

Result:

```js
user: {
  name: "Rahul"
}
```

---

# 39. ObjectId

MongoDB automatically creates an `_id`.

Example:

```js
{
  _id: ObjectId("68c...")
}
```

Find by `_id`:

```js
db.products.findOne({
  _id: ObjectId("68c...")
})
```

### Mongoose

You will commonly see:

```js
mongoose.Schema.Types.ObjectId
```

---

# 40. Indexes

Indexes improve query performance.

### Create index

```js
db.products.createIndex({
  name: 1
})
```

### View indexes

```js
db.products.getIndexes()
```

### Delete index

```js
db.products.dropIndex("name_1")
```

---

# 41. Unique Index

Useful for fields such as email.

```js
db.users.createIndex(
  {
    email: 1
  },
  {
    unique: true
  }
)
```

This prevents duplicate email values.

---

# 42. `explain()`

Used to understand how MongoDB executes a query.

```js
db.products.find({
  brand: "Nike"
}).explain("executionStats")
```

### Useful for

* Query optimization
* Checking index usage
* Understanding performance
* Learning how MongoDB executes queries

---

# 43. Quick Command Reference

| Category     | Commands / Operators | Main Purpose          |
| ------------ | -------------------- | --------------------- |
| Database     | `show dbs`           | Show databases        |
| Database     | `use`                | Switch database       |
| Database     | `db`                 | Show current database |
| Database     | `dropDatabase()`     | Delete database       |
| Collection   | `createCollection()` | Create collection     |
| Collection   | `show collections`   | Show collections      |
| Collection   | `drop()`             | Delete collection     |
| Create       | `insertOne()`        | Insert one            |
| Create       | `insertMany()`       | Insert many           |
| Read         | `find()`             | Find many             |
| Read         | `findOne()`          | Find one              |
| Filter       | `$eq`                | Equal                 |
| Filter       | `$ne`                | Not equal             |
| Filter       | `$gt`                | Greater than          |
| Filter       | `$gte`               | Greater/equal         |
| Filter       | `$lt`                | Less than             |
| Filter       | `$lte`               | Less/equal            |
| Filter       | `$in`                | Match any             |
| Filter       | `$nin`               | Exclude values        |
| Logic        | `$or`                | OR condition          |
| Logic        | `$and`               | AND condition         |
| Logic        | `$nor`               | NOT OR                |
| Logic        | `$not`               | Negate                |
| Output       | Projection           | Select fields         |
| Output       | `sort()`             | Sort results          |
| Output       | `limit()`            | Limit results         |
| Output       | `skip()`             | Skip results          |
| Count        | `countDocuments()`   | Count documents       |
| Update       | `updateOne()`        | Update one            |
| Update       | `updateMany()`       | Update many           |
| Update       | `$set`               | Set field             |
| Update       | `$inc`               | Increase/decrease     |
| Update       | `$unset`             | Remove field          |
| Update       | `$rename`            | Rename field          |
| Array        | `$push`              | Add to array          |
| Array        | `$pull`              | Remove from array     |
| Array        | `$addToSet`          | Add unique value      |
| Delete       | `deleteOne()`        | Delete one            |
| Delete       | `deleteMany()`       | Delete many           |
| Search       | `$regex`             | Pattern/text search   |
| Search       | `$exists`            | Check field existence |
| Search       | `distinct()`         | Unique values         |
| Aggregation  | `aggregate()`        | Data processing       |
| Aggregation  | `$match`             | Filter                |
| Aggregation  | `$group`             | Group                 |
| Aggregation  | `$sum`               | Sum/count             |
| Aggregation  | `$avg`               | Average               |
| Aggregation  | `$min`               | Minimum               |
| Aggregation  | `$max`               | Maximum               |
| Aggregation  | `$sort`              | Sort                  |
| Aggregation  | `$limit`             | Limit                 |
| Aggregation  | `$project`           | Shape output          |
| Aggregation  | `$multiply`          | Multiply values       |
| Relationship | `$lookup`            | Join-like operation   |
| Relationship | `$unwind`            | Flatten arrays        |
| Index        | `createIndex()`      | Create index          |
| Index        | `getIndexes()`       | View indexes          |
| Index        | `dropIndex()`        | Remove index          |
| Performance  | `explain()`          | Analyze query         |

---

# 44. Most Important Commands to Learn First

Don't try to memorize everything immediately.

### Level 1 — Must Know

```text
use
show dbs
show collections

insertOne()
insertMany()

find()
findOne()

updateOne()
updateMany()

deleteOne()
deleteMany()
```

### Level 2 — Querying

```text
$gt
$gte
$lt
$lte
$in
$nin
$or

sort()
limit()
skip()

projection
countDocuments()
```

### Level 3 — Data Manipulation

```text
$set
$inc
$unset
$rename

$push
$pull
$addToSet

$regex
$exists
distinct()
```

### Level 4 — Advanced

```text
aggregate()

$match
$group
$sum
$avg
$min
$max
$sort
$limit
$project
$multiply

$lookup
$unwind
```

### Level 5 — Performance

```text
createIndex()
getIndexes()
dropIndex()
explain()
```

---

# 45. MongoDB Learning Flow

```text
                    MongoDB
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      CREATE          READ          UPDATE
        │              │              │
   insertOne()       find()       updateOne()
   insertMany()      findOne()    updateMany()
                       │           $set
                       │           $inc
                       │           $unset
                       │
                       ↓
                    DELETE
                       │
                  deleteOne()
                  deleteMany()

                       ↓
                  QUERYING
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
    Operators        Sorting       Pagination
    $gt/$gte         sort()        skip()
    $lt/$lte         limit()       limit()
    $in/$nin

                       ↓
                   ARRAYS
                       │
              $push / $pull
               $addToSet

                       ↓
                 AGGREGATION
                       │
       $match → $group → $sort → $project
                       │
                $sum / $avg
                $min / $max

                       ↓
                RELATIONSHIPS
                       │
                  $lookup
                  $unwind

                       ↓
                  PERFORMANCE
                       │
                createIndex()
                  explain()
```

---

# 46. E-Commerce MongoDB Operations

For an e-commerce application, these are the operations you'll use frequently:

| Requirement                   | MongoDB Operation                   |
| ----------------------------- | ----------------------------------- |
| Add product                   | `insertOne()` / Mongoose `create()` |
| Get all products              | `find()`                            |
| Get one product               | `findOne()`                         |
| Get product by ID             | `_id` + `findOne()`                 |
| Search products               | `$regex`                            |
| Filter by category            | `{ category: "Shoes" }`             |
| Filter by price               | `$gte`, `$lte`                      |
| Filter by multiple conditions | Multiple fields / `$and`            |
| Sort by price                 | `sort()`                            |
| Pagination                    | `skip()` + `limit()`                |
| Count products                | `countDocuments()`                  |
| Update product                | `updateOne()`                       |
| Change stock                  | `$inc`                              |
| Activate/deactivate           | `$set`                              |
| Delete product                | `deleteOne()`                       |
| Get categories                | `distinct()`                        |
| Sales calculations            | `aggregate()`                       |
| Join orders/users             | `$lookup`                           |
| Improve search performance    | `createIndex()`                     |

---

# ⭐ The Core Mental Model

When writing a MongoDB query, ask yourself:

```text
1. WHAT collection?
        ↓
2. WHAT documents do I want?
        ↓
3. WHAT conditions?
        ↓
4. WHAT fields should I return?
        ↓
5. HOW should I sort?
        ↓
6. HOW MANY?
        ↓
7. SHOULD I SKIP ANY?
```

Example:

> Give me active Nike products costing between ₹2,000–₹10,000, highest price first, first 10 products.

```js
db.products.find({
  brand: "Nike",
  isActive: true,
  price: {
    $gte: 2000,
    $lte: 10000
  }
})
.sort({
  price: -1
})
.limit(10)
```

This single query combines the concepts you need most often in a real Express/Mongoose backend.
