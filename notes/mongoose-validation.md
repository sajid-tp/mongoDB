## Full Breakdown by Mongoose Method

| Method | Needs `new: true`? | Needs `runValidators: true`? | What it Returns by Default |
|---|---|---|---|
| `findByIdAndUpdate()` | **YES** | **YES** | Old document (before update) |
| `findOneAndUpdate()` | **YES** | **YES** | Old document (before update) |
| `findOneAndReplace()` | **YES** | **YES** | Old document (before replace) |
| `updateOne()` | ❌ *Not applicable* | **YES** | Update metadata (`acknowledged`, `modifiedCount`, etc.) |
| `updateMany()` | ❌ *Not applicable* | **YES** | Update metadata (`acknowledged`, `modifiedCount`, etc.) |
| `User.create()` | ❌ *Not needed* | ❌ *Already automatic* | Brand new saved document |
| `doc.save()` | ❌ *Not needed* | ❌ *Already automatic* | Updated/created document |
| `insertMany()` | ❌ *Not needed* | ❌ *Already automatic* | Array of inserted documents |
