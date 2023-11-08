# esbuild-bitburner-plugin

This is an ESBuild plugin that uses Bitburners remote API to push the build results into the game.

If you are looking for a ready to go template for your workspace, have a look at [bb-external-editor](https://github.com/NilsRamstoeck/bb-external-editor).

## How to use

```js
const createContext = async () => await context({
  entryPoints: [
    'servers/**/*.js',
    'servers/**/*.jsx',
    'servers/**/*.ts',
    'servers/**/*.tsx',
  ],
  outbase: "./servers",
  outdir: "./build",
  plugins: [BitburnerPlugin({
    port: 12525,
    types: 'NetscriptDefinitions.d.ts'
  })],
  bundle: true,
  format: 'esm',
  platform: 'browser',
  logLevel: 'info'
});

let ctx = await createContext();
ctx.watch();
```

## Using React

This plugin allows you to use the ingame instances of `React` and `ReactDOM` simply by importing them as ESModule as you usually would.

```jsx
import React, {useState} from 'react';

export MyComponent(){
  const [count, setCount] = useState(0);

  return <div>Count {count} <button onClick={() => setCount(count + 1)}>Add to count</button></div>;
}

```

## Uploading into the game

The output folder structure determines to which ingame server each file is sent to.
So if the transpilation results in the following structure:

```txt
dist
  ├──home
  │   └───homeScript.js
  └──otherServer
      └───otherScript.js
```

then `homeScript.js` will be uploaded to `home` and `otherScript.js` to `otherServer`.

## Options

### Port

The port that the RemoteAPI Server will listen on. This is the same port that you need to enter inside Bitburner to connect to the Plugin. default is `12525`.

### Types

This is the path that the Netscript Definitions file will be placed at. This is optional.

### Mirror

This enables file mirroring. You can use this to map remote servers to a local path like this:

```js
const createContext = async () => await context({
  entryPoints: [
    'servers/**/*.js',
    'servers/**/*.jsx',
    'servers/**/*.ts',
    'servers/**/*.tsx',
  ],
  outbase: "./servers",
  outdir: "./build",
  plugins: [BitburnerPlugin({
    port: 12525,
    types: 'NetscriptDefinitions.d.ts',
    mirror: {
      'local/path': ['home', 'and/or other servers']
    }
  })],
  bundle: true,
  format: 'esm',
  platform: 'browser',
  logLevel: 'info'
});

let ctx = await createContext();
ctx.watch();
```

Any file on home would then be placed in `local/path/home` and other servers in their respective directories.
Any changes made locally will then be synced into the game and any changes made in the game will also be synced locally.

### Distribute

This enables automatic distribution of files in a folder to multiple servers. For example, you can select a folder in 'build' to distribute scripts automatically once built like this

```js
const createContext = async () => await context({
  entryPoints: [
    'servers/**/*.js',
    'servers/**/*.jsx',
    'servers/**/*.ts',
    'servers/**/*.tsx',
  ],
  outbase: "./servers",
  outdir: "./build",
  plugins: [BitburnerPlugin({
    port: 12525,
    types: 'NetscriptDefinitions.d.ts',
    distribute: {
      'build/home/dist': ['server-1', 'server-2', 'server-3']
    }
  })],
  bundle: true,
  format: 'esm',
  platform: 'browser',
  logLevel: 'info'
});

let ctx = await createContext();
ctx.watch();

```

now all files that are developed in 'servers/home/dist' will not only be uploaded to 'home' but also 'server-1', 'server-2' and 'server-3'.
