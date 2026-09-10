For example, 
```javascript
db.products.find({ price: { $gt: 100 } })
```
With Mongoose, you write:
```javascript
Product.find({ price: { $gt: 100 } })
```
The MongoDB query condition is still:
```javascript
{ price: { $gt: 100 } }
```

### Mongoose gives you things like:

- Model.find()  
- Model.findOne()  
- Model.create()  
- Model.findById()  
- Model.findByIdAndUpdate()  
- populate()  
- Schema validation
- Middleware/hooks

What actually happens on that line
```javascript
const user = await User.create({ name, email, password: hashedPassword });
```
- Mongoose validates this data against your User schema (required fields, types, etc.).
- It inserts a new document into the users collection in MongoDB.
- MongoDB auto-generates a unique _id for it.
- Mongoose wraps the inserted data (including that new _id, plus any schema defaults like createdAt/updatedAt if you have timestamps enabled) into a Mongoose Document object and returns it.
await unwraps the promise, so user ends up holding that document directly — not a promise, not raw JSON.
- The nuance: it's not quite a plain JS object

This is the part worth knowing. What you get back is a Mongoose Document instance, which looks and mostly behaves like a plain object (user.email, user.name work as expected), but it also carries extra Mongoose-specific stuff attached to it:

```javascript
console.log(user);
// {
//   _id: new ObjectId('64f...'),
//   name: 'Sajid',
//   email: 'sajid@example.com',
//   password: '$2b$10$...',   ← the hashed one, since that's what you passed in
//   createdAt: ...,
//   updatedAt: ...,
//   __v: 0
// }
```
- It has built-in methods you don't get on a plain object — user.save() (to persist further changes), user.toJSON(), user.toObject(), schema-defined virtuals/methods if you added any, etc.
