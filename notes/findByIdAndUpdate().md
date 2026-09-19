## `findByIdAndUpdate()` Positional Breakdown

```js
Model.findByIdAndUpdate(id, update, options, callback)
```

Here is what each position strictly requires:

### The Positional Breakdown

| Position | Argument | Required? | What it does | Your Code |
| :--- | :--- | :--- | :--- | :--- |
| **1st** | `id` | **Yes** | The document's `_id` (`WHERE _id = ...`). Can be a string, number, or `ObjectId`. | `userId` |
| **2nd** | `update` | **Yes** | An object containing the fields to update (`SET ...`). | `{ name: name.trim(), phone: phone.trim() }` |
| **3rd** | `options` | Optional | Configuration flags modifying behavior (e.g., `new`, `runValidators`, `upsert`). | `{ new: true, runValidators: true }` |
| **4th** | `callback` | Optional *(Deprecated)* | Legacy Node-style callback `(err, doc) => {}`. Not used with `await`. | — |
