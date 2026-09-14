# Mongoose-Specific Queries & Features (Not in Plain MongoDB)

Mongoose is an ODM (Object Data Modeling) library built on top of the native MongoDB driver. It adds a schema layer and a bunch of convenience methods/query helpers that **don't exist in raw MongoDB** — the native driver only gives you `find`, `findOne`, `insertOne`, `updateOne`, `deleteOne`, etc.

---

## 1. ID-based shortcut methods

MongoDB driver has no special "by id" methods — you always pass `{ _id: ... }` to a normal query. Mongoose adds shortcuts:

| Mongoose method | Equivalent raw MongoDB |
|---|---|
| `Model.findById(id)` | `db.collection.findOne({ _id: id })` |
| `Model.findByIdAndUpdate(id, update)` | `db.collection.findOneAndUpdate({ _id: id }, update)` |
| `Model.findByIdAndDelete(id)` | `db.collection.findOneAndDelete({ _id: id })` |

```js
const user = await User.findById(req.params.id);
```

## 2. `findOneAndUpdate` / `findOneAndDelete` / `findOneAndReplace`

MongoDB driver has these too, but Mongoose's versions run **schema validation, casting, and middleware (hooks)** on top — raw MongoDB just mutates the document with no validation.

```js
const updated = await User.findOneAndUpdate(
  { email },
  { isBlocked: true },
  { new: true } // return the updated doc, not the original (Mongoose-specific option)
);
```

> `{ new: true }` is a Mongoose/driver option — without it, `findOneAndUpdate` returns the **old** document by default.

## 3. Chainable Query Builder

Mongoose queries are **not promises by default** — they're `Query` objects that are chainable AND thenable (awaitable). This lets you build queries step by step:

```js
const users = await User.find({ isBlocked: false })
  .where("age").gt(18)
  .select("name email")
  .sort({ createdAt: -1 })
  .skip(10)
  .limit(10);
```

Raw MongoDB driver requires all of this to go inside the filter/options object at once — no fluent chaining like `.where().gt()`.

## 4. `populate()` — the big one

**This does not exist in MongoDB at all.** MongoDB has no joins for normal collections (aggregation `$lookup` is the closest raw equivalent). Mongoose's `populate()` replaces a `ref` field with the actual referenced document, using separate queries under the hood.

```js
// Schema
const orderSchema = new Schema({
  user: { type: Schema.Types.ObjectId, ref: "User" }
});

// Query
const order = await Order.findById(id).populate("user");
// order.user is now the full User document, not just an ObjectId
```

You can populate nested paths, multiple fields, and even filter/select within the populate:
```js
await Order.find().populate({ path: "user", select: "name email" });
```

## 5. `lean()`

Mongoose docs are normally full Mongoose Documents (with getters, virtuals, methods, change-tracking) — heavier than plain objects. `.lean()` tells Mongoose to skip all that and return plain JS objects, closer to what raw MongoDB returns. Faster for read-only operations.

```js
const users = await User.find().lean(); // plain JS objects, no Mongoose overhead
```

## 6. Virtuals

Fields that exist on the Mongoose document but **aren't stored in MongoDB at all**.

```js
userSchema.virtual("fullName").get(function () {
  return `${this.firstName} ${this.lastName}`;
});
```
Raw MongoDB has no concept of a computed, non-persisted field like this.

## 7. Middleware / Hooks (`pre` / `post`)

Mongoose lets you hook into lifecycle events on documents/queries — MongoDB itself has no hook system; every hook runs in your app layer.

```js
userSchema.pre("save", async function (next) {
  if (this.isModified("password")) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

userSchema.post("findOneAndUpdate", function (doc) {
  console.log("Updated:", doc);
});
```

Common hook types: `save`, `validate`, `findOneAndUpdate`, `deleteOne`, `remove`, `init`, `aggregate`.

## 8. Schema Validation & Casting

MongoDB is schema-less by default — no field types are enforced at the DB level unless you use JSON Schema validators manually. Mongoose enforces types, `required`, `min`/`max`, `enum`, `match` (regex), and custom validators automatically before a document is saved, and **casts** input to the schema type (e.g., a string `"5"` becomes Number `5` if the schema says `Number`).

```js
const userSchema = new Schema({
  email: { type: String, required: true, match: /.+@.+\..+/ },
  age: { type: Number, min: 0, max: 120 },
  role: { type: String, enum: ["user", "admin"], default: "user" }
});
```

## 9. Instance Methods & Statics

Mongoose lets you attach custom methods directly to documents or to the Model — not a MongoDB concept at all.

```js
// Instance method (called on a document)
userSchema.methods.comparePassword = function (candidate) {
  return bcrypt.compare(candidate, this.password);
};
user.comparePassword("input123");

// Static method (called on the Model)
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};
User.findByEmail("a@b.com");
```

## 10. `Model.exists()`

Returns just `{ _id }` or `null` instead of the full document — a lightweight existence check. (MongoDB driver has no dedicated `exists`; you'd normally do a `findOne` with projection or `countDocuments`.)

```js
const alreadyRegistered = await User.exists({ email });
```

## 11. `countDocuments()` vs `estimatedDocumentCount()`

Both exist in the raw driver too, but Mongoose exposes them with query-chaining:
- `countDocuments(filter)` — accurate, scans based on filter.
- `estimatedDocumentCount()` — fast, uses collection metadata, ignores filters entirely.

## 12. Document instance methods

Every Mongoose document (not query result — an actual `new Model()` or fetched doc) has built-in helpers with no MongoDB equivalent:
- `doc.isModified("field")` — check if a field changed since fetch
- `doc.isNew` — true if not yet saved
- `doc.toObject()` / `doc.toJSON()` — convert to plain object (drops Mongoose-specific stuff)
- `doc.save()` — insert or update depending on `isNew`, runs validation + hooks
- `doc.populate()` — populate on an already-fetched document

## 13. Discriminators

Mongoose's way of doing schema inheritance in a single collection — no MongoDB equivalent.

```js
const Animal = mongoose.model("Animal", animalSchema);
const Dog = Animal.discriminator("Dog", dogSchema); // shares collection, different schema
```

## 14. Auto-generated indexes from schema

Declaring `unique: true` or `index: true` in a schema tells Mongoose to build the index automatically on startup — in raw MongoDB you'd run `createIndex()` yourself.

```js
email: { type: String, unique: true }
```

---

## Quick summary table

| Feature | Mongoose | Raw MongoDB Driver |
|---|---|---|
| `findById` | ✅ | ❌ (use `findOne({_id})`) |
| `populate()` (joins) | ✅ | ❌ (use `$lookup` aggregation) |
| Schema validation | ✅ automatic | ❌ manual / JSON Schema |
| Type casting | ✅ automatic | ❌ |
| Virtuals | ✅ | ❌ |
| Hooks (`pre`/`post`) | ✅ | ❌ |
| Chainable query builder | ✅ | ⚠️ limited |
| `.lean()` | ✅ | N/A (driver docs are already plain objects) |
| Instance/static methods | ✅ | ❌ |
| Discriminators | ✅ | ❌ |
