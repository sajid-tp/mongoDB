## `findByIdAndUpdate()` Positional Breakdown

```js
Model.findByIdAndUpdate(id, update, options, callback)
Here is what each position strictly requires:
The Positional Breakdown
Position	Argument	Required?	What it does	Your Code
1st	id	Yes	The document's _id (WHERE _id = ...). Can be a string, number, or ObjectId.	userId
2nd	update	Yes	An object containing the fields to update (SET ...).	{ name: name.trim(), phone: phone.trim() }
3rd	options	Optional	Configuration flags modifying behavior (e.g. new, runValidators, upsert).	{ new: true, runValidators: true }
4th	callback	Optional (Deprecated/Legacy)	Legacy Node-style callback (err, doc) => {}. Not used when using await.	—



**Important:** Because the code block itself contains another code block, if you're putting this directly into a `.md` file, use **four backticks** for the outer code block:

````md
## `findByIdAndUpdate()` Positional Breakdown

```js
Model.findByIdAndUpdate(id, update, options, callback)
Here is what each position strictly requires:
The Positional Breakdown
Position	Argument	Required?	What it does	Your Code
1st	id	Yes	The document's _id (WHERE _id = ...). Can be a string, number, or ObjectId.	userId
2nd	update	Yes	An object containing the fields to update (SET ...).	{ name: name.trim(), phone: phone.trim() }
3rd	options	Optional	Configuration flags modifying behavior (e.g. new, runValidators, upsert).	{ new: true, runValidators: true }
4th	callback	Optional (Deprecated/Legacy)	Legacy Node-style callback (err, doc) => {}. Not used when using await.	—
````
