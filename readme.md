# Complete NodeJS Lessons
- run node console type `node` in terminal
- creating a node server with the code shown below, then run `node app.js`, then go to `localhost:3000` to see the server running. 
- `process` is a global variable
- `process.exit();` in code hard exits the programme
- Take note of [NodeJS Lifecycle](https://www.oreilly.com/library/view/nodejs-the/9781838826864/video3_4.html) 
- To parse request bodies, run `req.on("<something>", function())`
```
// app.js
const http = require("http");

const server = http.createServer((req, res) => {
  console.log(req.url, req.method, req.headers);
});

server.listen(3000);
```

## Debugging
- `npm init` to add "package.json". In "package.json", you can add commands into "scripts" for them to run. 
- `npm run <script_command>` to run commands. 
- "scripts" can be used to build workflows
- `npm install nodemon --save-dev` adds "node_modules" folder and "package-lock.json" file. With nodemon, there is no need to restart the app. It will keep the app open.
- Types of errors: syntax errors, runtime errors, logical errors. Logical errors are silent.
- To debug NodeJS logical errors. click "run" -> "start debugging"

## Working with Express JS on Node JS
- ExpressJS is easier to code on NodeJS
- Below is an example of getting started. For more info to go github/expressjs/express/lib/response.js
```
const express = require('express');
const app = express();
app.use("/<path>", () => {}) // for post and get requests
app.get("/<path>", () => {}) // for get requests
app.post("/<path>", () => {}) // for post requests
app.delete("/<path>", () => {}) // for delete requests
app.patch("/<path>", () => {}) // for patch requests
app.put("/<path>", () => {}) // for put requests
app.listen(3000);
```
- use `const bodyParser = require('body-parser');` to parse incoming body parse requests `res.send(<some html code>)` in `app.use()`
- use `const Router = express.Router();` example. `router.get()` functions similarly to `app.get()`
```
const path = require('path');
const express = require('express');
const rootDir = require('../util/path');
const router = express.Router();
router.get('/', (req, res, next) => {
  res.sendFile(path.join(rootDir, 'views', 'shop.html'));
});
module.exports = router;
//remember to import this to app.js file with `const shopRoutes = require('./routes/shop');`
```
- Below is example code to show views from express js
```
// /routes/admin.js
const path = require('path');
const express = require('express');
const rootDir = require('../util/path');
const router = express.Router();
// /admin/add-product => GET
router.get('/add-product', (req, res, next) => {
  res.sendFile(path.join(rootDir, 'views', 'add-product.html'));
});

// /util/path.js
const path = require('path');
module.exports = path.dirname(process.mainModule.filename);
```
- use `app.use(express.static(path.join(__dirname, 'public')));` in app.js to then in the .html file, add `<link rel="stylesheet" href="/css/main.css">` to get the css files loaded.

## Working on dynamic content with ExpressJS
- Handlebars, pug and ejs are used as the main templating engine. Useful resources:
  - Pug Docs: [https://pugjs.org/api/getting-started.html](https://pugjs.org/api/getting-started.html)
  - Handlebars Docs: [https://handlebarsjs.com/](https://pugjs.org/api/getting-started.html)
  - EJS Docs: [http://ejs.co/#docs](https://pugjs.org/api/getting-started.html)

- `app.set(name, value)` and/or `const expressHbs = require('express-handlerbars'); app.engine('handlerbars', expressHbs());` sets a value globally on ExpressJs application. Normally added to app.js
- `res.render(<file name>, <object value>)` renders a file accordig to the templating engine determined in app.js

## MVC with ExpressJS
- Models: Represents your data in your code. Work with your data
- Views: What the user sees. Decoupled from your application code
- Controller: Connecting your Models and your Views, Contains the "in between" logic. It is also is affected the paths determined by Routes.
- Create models, views, controllers and routes folders in the root directory
- Models will use a class, then Controllers will import from it from require
```
// /models/product.js
module.exports = class Product {
  constructor(t) {
    this.title = t;
  }

  save() {
    getProductsFromFile(products => {
      products.push(this);
      fs.writeFile(p, JSON.stringify(products), err => {
        console.log(err);
      });
    });
  }

  static fetchAll(cb) {
    getProductsFromFile(cb);
  }
};

// in controller
const Product = require('../models/product');
```

