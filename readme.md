# Complete NodeJS Lessons
- This tutorial covers building a fullstack app with NodeJS. However, plenty of times, we only need it to enable GraphQL or API.
- It is also useful for backend operations like authentication, email, sessions cookies etc.

## Setup
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

## Dyanmic Routes & Advanced Models
- All modals should have constructors and also static functions. Static functions are get all, find one, add, update, delete etc.
- Work flow as follows: url or forms in view with URL and call method -> routes -> controllers -> model -> controllers with response -> updated view
- When deleting product, have to ensure that other relational data are also deleted.
- You can pass dynamic path segments by adding a ":" to the routes file.
- The name you add after ":" in routes is the name by which you can extract the data on `req.params.< string after : >`
- Optional (query) parameters can also be passed (?myParam=value&b=2) and extracted (req.query.myParam).
- By call a function within a function, (e.g. delete cart item if a product is deleted), you can interact with models
```
  Product.findById(prodId, product => {
    Cart.deleteProduct(prodId, product.price);
    res.redirect('/cart');
  });
```


## Setting up database | SQL and NoSQL database

### MySQL
- first install MySQL into desktop.
- add database in "/util/database.js", then in the models, call `const db = require('../util/database')`, then execute sql code. Example as follows: `db.execute('SELECT * FROM products');`, then in models, use `.then().catch();` because a promise is used in "/util/database.js"

### Sequalize
- `npm install sequalize` Using Sequelize as Object Relational Mapping (ORM) for MySQL. So no need to write in SQL.
- To connect sequalize code in "/util/database.js" with code below. Also, add code in "/app.js"
```
// /util/database.js
const Sequelize = require('sequelize');

const sequelize = new Sequelize('node-complete', 'root', 'nodecomplete', {
  dialect: 'mysql',
  host: 'localhost'
});

module.exports = sequelize;

// /app.js
sequelize
  // .sync({ force: true }) // overwrite tables and drops conflicting tables
  .sync()
  .then(result => {
    app.listen(3000);
  })
  .catch(err => {
    console.log(err);
  });

```
- Associations is done with the code below:
```
// app.js
// associations are written in app.js
Product.belongsTo(User, { constraints: true, onDelete: 'CASCADE' });
User.hasMany(Product);
Cart.belongsToMany(Product, { through: CartItem });
Product.belongsToMany(Cart, { through: CartItem });

// /controllers/admin.js
// add associations with .user.createProduct
...
  req.user
    .createProduct({
      title: title,
      price: price,
      imageUrl: imageUrl,
      description: description
    })
...
```
- How to migrate with sequalize?

### MongoDB
- `npm install --save mongodb` 
- connect with mongodb in "/util/database.js"
- `mongodb` has its own ORM method
- To learn more about mongodb, go to mongodb website -> mongodb server -> mongodb CRUD operations to get all the examples
- This tutorial doesn't close connection. In real life, it is needed.
- Calling MongoDB database should always follow these steps: get to collection -> execute function using mongodb methods -> then console log results and return results -> else catch error.
```
db.collection('products')
  .find({ _id: new mongodb.ObjectId(prodId) })
  .next()
  .then(product => {
    console.log(product);
    return product;
  })
  .catch(err => {
    console.log(err);
  });
```
- in this tutorial, cart is added as a field in the users collection
- This tutorial contains comprehensive methods for CRUD in MongoDB

### Mongoose
- Mongoose is an Object Document Mapping Library (ODM)
- Setting up Mongoose is similar to MongoDB, only that it is written differently
- Schema method is used in Mongoose. Very similar to constructor. It saves us the trouble of creating methods in the object model constructor. i.e. Product class object has constructors and methods, refer to code above.
- it provides cursor() for things like data pagination
- Use code example below for relational setup for ODM
```
productId: {
  type: Schema.Types.ObjectId,
  ref: 'Product',
  required: true
},
```
- populate method is used to get related fields quickly and you can control what fields to get i.e. `-_id` means exclude `_id`
```
    // .select('title price -_id')
    // .populate('userId', 'name')
```


## Sessions and Cookies
- Cookies are stored in the frontend browser side, while sessions are stored in the backend server side, stored in the memory. Storing in memory is not a good idea, because you may run out of it when there are a lot of sessions. Later on, there is a library to store in mongodb database.
- Cookies (created by frontend) -> Hashed user ID -> Sessions (Stored in Memory or Database)
- `npm install --save express-session` to install session functions in expressJS
- using express-sessions example code below
```
// app.js
const session = require('express-session');

app.use(
  session({
    secret: 'my secret',
    resave: false,
    saveUninitialized: false,
    store: store
  })
);

// /controllers/auth.js
req.session.isLoggedIn = true;
```
- `npm install --save connect-mongodb-session` to store the session into mongodb rather than in memory.
- using mongodb-session example code below
```
// app.js
const MongoDBStore = require('connect-mongodb-session')(session);

const store = new MongoDBStore({
  uri: MONGODB_URI,
  collection: 'sessions'
});
```
- Data like whether a user is logged in a form of boolean `true` doesn't stick around in response (res), therefore it is important to keep it in a cookie.
- Setting cookies in NodeJS can be done with `res.setHeader("Set-Cookie", "loggedIn=true;");` with options for `Max-Age=10; HttpOnly; Secure;` etc. `HttpOnly` can be changed from the http, not from the client browser.
- Cookie in the frontend to store hash of the ID of the session, the session in the server will match it, then allow for access to private specific user only data
- To remove a cookie session. The cookie in the frontend will be removed and the session in the mongodb server will be deleted.
```
`req.session.destroy(err => {
  console.log(err);
  res.redirect('/');
})`
```


## Authentication
- Whenever a user calls for restricted access, we have to check if the user's cookie and match it with a session, before allowing for restricted access. Compare this with JWT for NextJS.
- When creating user data, encrypt password with `npm install --save bcryptjs` before saving it.
```
// /controllers/auth.js
const bcrypt = require('bcryptjs');

return bcrypt
  .hash(password, 12)
  .then(hashedPassword => {
    const user = new User({
      email: email,
      password: hashedPassword,
      cart: { items: [] }
    });
    return user.save();
  })
  .then(result => {
    res.redirect('/login');
  });
```
- Use a middleware for route protection, basically, it is a function method in a file, then in routes that needs to use it will import it and call it's method. This is so that if one change in the middleware, all others will change accordingly.
```
// middleware/is-auth.js
module.exports = (req, res, next) => {
  if (!req.session.isLoggedIn) {
    return res.redirect('/login');
  }
  next();
}

// routes/admin.js
const isAuth = require('../middleware/is-auth');

router.get('/add-product', isAuth, adminController.getAddProduct);
```
- To prevent CSRF attack, use CSRF token. User led to Fake Site that looks like our site, then sends malicious action (put or create) to your NodeJS server. To prevent this, we have to make sure that users requests are coming from our views rendered by our application.
- ATTENTION: csurf is no longer maintained
- `npm install --save csurf`. Package for node express that generates a token that we can embed in our request for things that changes the users state like forms.
```
// /app.js
const csrf = require('csurf');
const csrfProection = csrf();
app.use(csrfProtection);

app.use((req, res, next) => {
  res.locals.isAuthenticated = req.session.isLoggedIn;
  res.locals.csrfToken = req.csrfToken(); // this is needed so that all CSRF token is initiated when our view is loaded.
  next();
});

// in ejs forms
<input type="hidden" name="_csrf" value="<%= csrfToken %>">
```
- `npm install --save connect-flash` to provide user feedback like notifications when something goes wrong. Then add it in the front end
```
// /controllers/auth.js
req.flash('error', 'Invalid email or password.');
```
- When resetting password, use goes to form -> type in email -> generate crypto.randomBytes -> send email with crypto token link -> check to ensure crypto token link has not expired -> postNewPassword form page -> create new password


## Sending mail
- `npm install --save nodemailer nodemailer-sendgrid-transport`
```
// /controller/auth.js
const transporter = nodemailer.createTransport(
  sendgridTransport({
    auth: {
      api_key:
        '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'
    }
  })
);

return transporter.sendMail({
  to: email,
  from: 'shop@node-complete.com',
  subject: 'Signup succeeded!',
  html: '<h1>You successfully signed up!</h1>'
});
```


## Validation Process
- Validation ideally should be done client side, then validate on server side. If either fails, send error message. Some times database has built in validation
- `npm install --save express-validator` for NodeJS. for more validator methods go to `validator.js`
```
// /routes/auth.js
const { body } = require("express-validator/check");

router.post(
  '/add-product',
  [
    body('title')
      .isString()
      .isLength({ min: 3 })
      .trim(),
    body('imageUrl').isURL(),
    body('price').isFloat(),
    body('description')
      .isLength({ min: 5, max: 400 })
      .trim()
  ],
  isAuth,
  adminController.postAddProduct
);

// /controllers/auth.js

const { validationResult } = require('express-validator/check');

const errors = validationResult(req);
if (!errors.isEmpty()) {
  return res.status(422).render('auth/login', {
    path: '/login',
    pageTitle: 'Login',
    errorMessage: errors.array()[0].msg,
    oldInput: {
      email: email,
      password: password //this is to keep old inputs
    },
    validationErrors: errors.array()
  });
}

// /views/auth/signup.js
// change css invalid class to show red colour for invalid class
<input 
    class="<%= validationErrors.find(e => e.param === 'password') ? 'invalid' : '' %>"
    type="password" 
    name="password" 
    id="password" 
    value="<%= oldInput.password %>">
```
- inputs like blank spaces can be sanitized with `trim();` during validation


## Error Handling
- There are three type of errors: Technical/network errors, expected errors, bugs/logical errors
- Error is thrown: synchronous code with `try-catch` and asynchronous code with `then()-catch()`.
- No error is thrown: validate values where error is thrown or direcly handle errors. Then show error page (e.g. 500 page), intended page/response with error information and redirect.
- instead of redirect to 500 page, we can throw an error object
```
// /controllers/admin.js
  .catch(err => {
    const error = new Error(err);
    error.httpStatusCode = 500;
    return next(error);
  });

// /app.js
app.get('/500', errorController.get500);

app.use((error, req, res, next) => { //this is to handle 500 error in app.js by renderring 500 error page rather than redirecting to 500, cause it will get stuck in an infinite loop.
  // res.status(error.httpStatusCode).render(...);
  // res.redirect('/500');
  res.status(500).render('500', {
    pageTitle: 'Error!',
    path: '/500',
    isAuthenticated: req.session.isLoggedIn
  });
});
```
- when succeeded, need to respond with proper code. i.e. 200 when operations succeed, 201 when something created, 404 when page not found, 500 when server fails


## File uploads and downloads
- ATTENTION: the method proposed here is to store file in the server and not some third party cloud server.
- Handle multipart form data with `npm install --save multer` then add the following code to front end
```
// /views/admin/edit.js
<form class="product-form" action="/admin/<% if (editing) { %>edit-product<% } else { %>add-product<% } %>" method="POST" enctype="multipart/form-data">

// /app.js

// configure disk storage
const fileStorage = multer.diskStorage({ 
  destination: (req, file, cb) => {
    cb(null, 'images');
  },
  filename: (req, file, cb) => {
    cb(null, new Date().toISOString() + '-' + file.originalname);
  }
});

// filter file type
const fileFilter = (req, file, cb) => { 
  if (
    file.mimetype === 'image/png' ||
    file.mimetype === 'image/jpg' ||
    file.mimetype === 'image/jpeg'
  ) {
    cb(null, true);
  } else {
    cb(null, false);
  }
};

// handling file uploads with multer
app.use( 
  multer({ storage: fileStorage, fileFilter: fileFilter }).single('image')
);
```
- to serve the image files
```
app.use('/images', express.static(path.join(__dirname, 'images')));
```
- to get pdf, refer to `exports.getInvoice`
- dont `fs.readFile` cause it takes a lot of memory, causing overflow. better to stream data with `fs.createReadStream`
- use PDFKit to create PDF dynamically `npm install --save pdfkit`. note that it uses coffee script.
- to delete file locally use `fs.unlink`


## Pagination
- implementing pagination from scratch
- in mongoDB and mongoose, first skip the first x amount of results, then limit the amount that you retrieve. code example, 
```
.then(numProducts => {
  totalItems = numProducts; // this declared the total number of products
  return Product.find()
    .skip((page - 1) * ITEMS_PER_PAGE)
    .limit(ITEMS_PER_PAGE);
})
```
- then when trying to render in controlers
```
// /controllers/shop.js
res.render('shop/product-list', {
  prods: products,
  pageTitle: 'Products',
  path: '/products',
  currentPage: page,
  hasNextPage: ITEMS_PER_PAGE * page < totalItems,
  hasPreviousPage: page > 1,
  nextPage: page + 1,
  previousPage: page - 1,
  lastPage: Math.ceil(totalItems / ITEMS_PER_PAGE)
});

// then refer to /includes/pagination.ejs

```


## Asynchronous code in NodeJS
- It is not very user friendly but, if you must, refer to code in "/public/js/admin.js"


## Payment Process
- Collect payment method, verify payment method, charge payment method, manage payments, process order in app
- `npm install --save stripe`
- on front end checkout add this script
```
// /view/shop/checkout.ejs
<button id="order-btn" class="btn">ORDER</button>
<script src="https://js.stripe.com/v3/"></script>
<script>
    var stripe = Stripe('pk_test_Oa1aEfV1EFTGOLJHIkx6mFUs00AtOsluFy');
    var orderBtn = document.getElementById('order-btn');
    orderBtn.addEventListener('click', function() {
        stripe.redirectToCheckout({
            sessionId: '<%= sessionId %>'
        });
    });
</script>
```
- in controller adjust the code
```
// /controllers/shop.js
const stripe = require('stripe')('sk_test_Flc1Upp19T0q8ZgmKGDVJUI400j9emUSTr');

// in exports.getCheckout
return stripe.checkout.sessions.create({
  payment_method_types: ['card'],
  line_items: products.map(p => {
    return {
      name: p.productId.title,
      description: p.productId.description,
      amount: p.productId.price * 100,
      currency: 'usd',
      quantity: p.quantity
    };
  }),
  success_url: req.protocol + '://' + req.get('host') + '/checkout/success', // => http://localhost:3000
  cancel_url: req.protocol + '://' + req.get('host') + '/checkout/cancel'
});

```
- go to stripe One-time Payments in their documents for more information
- ATTENTION: there are no webhooks. It is needed to check for success