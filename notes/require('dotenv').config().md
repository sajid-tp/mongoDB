- require('dotenv') loads the package
`require('dotenv')` loads the `dotenv` package (the one you installed with `npm install dotenv`). This package's whole job is reading `.env` files.
- .config() actually triggers the reading of your .env file
Calling `.config()` immediately on it tells dotenv: 'find my `.env` file (it looks in the project root by default) and read it right now.'
- It parses each KEY=VALUE line and injects it into process.env
dotenv reads each line of your `.env` file — like `PORT=8080` and `MONGO_URI=mongodb://localhost:27017/stepSelect` — and injects them into Node's global `process.env` object, as if you'd set them as actual environment variables on your system.
- This is why process.env.MONGO_URI and process.env.PORT work
That's exactly why your `db.js` file can do `process.env.MONGO_URI` and get the actual connection string, and `server.js` can do `process.env.PORT` and get `8080` — dotenv is the bridge that makes your `.env` file's plain text values available as real JavaScript values inside your running code.
- Why it has to be at the very TOP of server.js
This line MUST run before any other code that uses `process.env.SOMETHING` — that's why it's at the very top of `server.js`. If you called `connectDB()` (which reads `process.env.MONGO_URI`) BEFORE this line ran, `process.env.MONGO_URI` would still be `undefined`.
