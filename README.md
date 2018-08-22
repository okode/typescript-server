# TypeScript Server

Simple TypeScript Server.

## Setting Up TypeScript Project

```
$ mkdir typscript-server
$ cd typescript-server
$ npm init -y
$ npm install --save-dev typescript nodemon ts-node @types/express @types/es6-shim
$ npm install --save express
```

Add script `tsc` to `package.json`:

```
"scripts": {
  "tsc": "tsc"
}
```

Initialize `tsconfig.json`:

```
$ npm run tsc -- --init
```

## Create source layout

```
$ mkdir src && cd src
$ touch server.ts
$ mkdir controllers && cd controllers
$ touch index.ts welcome.controller.ts
```

## Add source contents

src/server.ts

```
import * as express from 'express';

// Import WelcomeController from controllers entry point
import { WelcomeController } from './controllers';

// Create a new express application instance
const app: express.Application = express.default();

// The port the express app will listen on
const port = process.env.PORT || 3000;

// Mount the WelcomeController at the /welcome route
app.use('/welcome', WelcomeController);

// Serve the application at the given port
app.listen(port, () => {
    // Success callback
    console.log(`Listening at http://localhost:${port}/`);
});
```

src/controllers/index.ts

```
export * from './welcome.controller';
```

src/controllers/welcome.controller.ts

```
// Import only what we need from express
import { Router, Request, Response } from 'express';

// Assign router to the express.Router() instance
const router = Router();

// The / here corresponds to the route that the WelcomeController
// is mounted on in the server.ts file.
// In this case it's /welcome
router.get('/', (req: Request, res: Response) => {
    // Reply with a hello world when no name param is provided
    res.send('Hello, World!');
});

router.get('/:name', (req: Request, res: Response) => {
    // Extract the name from the request parameters
    let { name } = req.params;

    // Greet the given name
    res.send(`Hello, ${name}`);
});

// Export the express.Router() instance to be used by server.ts
export const WelcomeController = router;
```

## Add auto restart and debug support

Create `nodemon.json` with the following content:

```
{
    "ignore": ["**/*.test.ts", "**/*.spec.ts", ".git", "node_modules"],
    "watch": ["src"],
    "exec": "npm start",
    "ext": "ts"
}
```

Add the following scripts to `package.json`:

```
scripts: {
    "start": "node --inspect=5858 -r ts-node/register src/server.ts",
    "dev": "./node_modules/nodemon/bin/nodemon.js"
}
```

Create a .vscode/launch.json file with the following content:

```
{
    // Use IntelliSense para saber los atributos posibles.
    // Mantenga el puntero para ver las descripciones de los existentes atributos 
    // Para más información, visite: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "attach",
            "name": "Attach",
            "port": 5858,
            "restart": true,
            "protocol": "inspector"
        }
    ]
}
```

## Running with auto restart and debug support

```
$ npm install
$ npm run dev
```

From VS Code attach to process Node and debug.