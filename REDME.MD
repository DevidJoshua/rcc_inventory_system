# Setup Boilerplate Setup

Setup Node.js package.json

Using the `-y` flag when creating a `package.json` will simply approve all the defaults.

```bash
npm init -y
```

### Add TypeScript as a dev dependency

This probably doesn't come as a surprise ;)

```bash
npm install typescript --save-dev
```

After we install `typescript`, we get access to the command line TypeScript compiler through the `tsc` command. More on that below.

### Install ambient Node.js types for TypeScript

TypeScript has Implicit, Explicit, and *Ambient* types. Ambient types are types that get added to the global execution scope. Since we're using Node, it would be good if we could get type safety and auto-completion on the Node apis like `file`, `path`, `process`, etc. That's what installing the [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) type definition for Node will do.

```bash
npm install @types/node --save-dev
```

### Create a `tsconfig.json`.

The `tsconfig.json` is where we define the TypeScript compiler options. We can create a tsconfig with several options set.

```bash
npx tsc --init --rootDir src --outDir build \
--esModuleInterop --resolveJsonModule --lib es6 \
--module commonjs --allowJs true --noImplicitAny true
```

* `rootDir`: This is where TypeScript looks for our code. We've configured it to look in the `src/` folder. That's where we'll write our TypeScript.
* `outDir`: Where TypeScript puts our compiled code. We want it to go to a `build/` folder.
* `esModuleInterop`: If you were in the JavaScript space over the past couple of years, you might have recognized that modules systems had gotten a little bit out of control (AMD, SystemJS, ES Modules, etc). For a topic that requires a much longer discussion, if we're using `commonjs` as our module system (for Node apps, you should be), then we need this to be set to `true`.
* `resolveJsonModule`: If we use JSON in this project, this option allows TypeScript to use it.
* `lib`: This option adds *ambient* types to our project, allowing us to rely on features from different Ecmascript versions, testing libraries, and even the browser DOM api. We'd like to utilize some `es6` language features. This all gets compiled down to `es5`.
* `module`: `commonjs` is the standard Node module system in 2019. Let's use that.
* `allowJs`: If you're converting an old JavaScript project to TypeScript, this option will allow you to include `.js` files among `.ts` ones.
* `noImplicitAny`: In TypeScript files, don't allow a type to be unexplicitly specified. Every type needs to either have a specific type or be explicitly declared `any`. No implicit `any`s.


### Cold reloading

Cold reloading is nice for local development. In order to do this, we'll need to rely on a couple more packages: `ts-node` for running TypeScript code directly without having to wait for it be compiled, and `nodemon`, to watch for changes to our code and automatically restart when a file is changed.

```bash
npm install --save-dev ts-node nodemon
```

Add a `nodemon.json` config.

```json
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "npx ts-node ./src/index.ts"
}
```

And then to run the project, all we have to do is run `nodemon`. Let's add a script for that.

```json
"start:dev": "npx nodemon",
```

By running `npm run start:dev`, `npx nodemon` will start our app using `npx ts-node ./src/index.ts`, watching for changes to `.ts` and `.js` files from within `/src`.

### Creating production builds

In order to *clean* and compile the project for production, we can add a `build` script.

Install `rimraf`, a cross-platform tool that acts like the `rm -rf` command (just obliterates whatever you tell it to).

```bash
npm install --save-dev rimraf
```

And then, add this to your `package.json`.

```json
"build": "rimraf ./build && tsc",
```

Now, when we run `npm run build`, `rimraf` will remove our old `build` folder before the TypeScript compiler emits new code to `dist`.
