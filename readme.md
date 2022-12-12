# Complete NodeJS Lessons
- run node console type `node` in terminal
- creating a node server with the code shown below, then run `node app.js`, then go to `localhost:3000` to see the server running. 
- `process.exit();` in code hard exits the programme.
- Info on NodeJS Life Cycle 
![NodeJS LifeCycle](/public/Node_Lifecycle_Event_Loop.png)
```
// app.js
const http = require("http");

const server = http.createServer((req, res) => {
  console.log(req);
});

server.listen(3000);
```

