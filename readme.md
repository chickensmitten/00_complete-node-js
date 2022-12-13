# Complete NodeJS Lessons
- run node console type `node` in terminal
- creating a node server with the code shown below, then run `node app.js`, then go to `localhost:3000` to see the server running. 
- `process.exit();` in code hard exits the programme.
- Info on NodeJS Life Cycle 
![NodeJS LifeCycle](/public/Node_Lifecycle_Event_Loop.png)
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

## Using Express JS on Node JS
- ExpressJS is easier to code on
- 