For example, 
```mongoDB
db.products.find({ price: { $gt: 100 } })

With Mongoose, you write:

Product.find({ price: { $gt: 100 } })

The MongoDB query condition is still:

{ price: { $gt: 100 } }

```

### Mongoose gives you things like:

Model.find()
Model.findOne()
Model.create()
Model.findById()
Model.findByIdAndUpdate()
populate()
Schema validation
Middleware/hooks
