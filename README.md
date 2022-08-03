# webhooks + body-parser + Node + Express :  Problem Fix

Works for **Shopify**, **Stripe** and maybe more.

## Problem

When using Node.js with Express we configure our app using bodyParser
```javascript
const { bodyParser } = require('body-parser')
// Express app
const app = express();
app.use(bodyParser.json());
```
This makes it possible to parse incoming request bodies in a middleware before your handlers, available under the req.body property.

Now that you've already set your express app to use the bodyParser.json() middleware, this clashes with the webhook route because this one uses bodyParser.raw() middleware.

## Fix

An easy way to fix it is to remove the app.use(bodyParser.json()); line. It will work for your your webhook route, but will break the rest of your routes that use a parsed body.

Hence, this isn't ideal.

The simplest way would be to add a custom middleware to choose which bodyparser to use depending on the route of the request.
```javascript
app.use((req, res, next) => {
  if (req.originalUrl === '/webhooks') {
    next();
  } else {
    bodyParser.json()(req, res, next);
  }
});
```
It will then use bodyParser.raw() for the specified webhook routes.
 
## Useful links

https://expressjs.com/en/resources/middleware/body-parser.html

https://lorenzosfarra.com/2019/02/19/node-body-parser-and-shopify-webhooks-verification/

https://medium.com/@scottdixon/verifying-shopify-webhooks-with-nodejs-express-ac7845c9e40a

https://stackoverflow.com/questions/56145563/getting-exception-on-webhook-from-stripe#:~:text=It's%20because%20you've%20already,If%20you%20remove%20the%20app.
