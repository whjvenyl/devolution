<div align="center">
  <h1>DEvolution 🦖</h1>
  <br/>
  <img src="./assets/devo-logo.jpg" alt="devolution" width="409" align="center">
  <br/>
  <br/>
  de-evolution gun, as seen in Mario Bros, to help you ship modern, and de-modernized bundles. 
  <br/>
  <br/>
    <a href="https://www.npmjs.com/package/devolution">
      <img src="https://img.shields.io/npm/v/devolution.svg?style=flat-square" />
    </a>

</div>


## Why?
- ship more modern, more compact and more fast code to 85+% of your customers
- do not worry about transpiling node_modules - use as modern code as you can everywhere
- don't be bound to the bundler

- uses [swc](https://github.com/swc-project/swc) to be a blazing 🔥 fast! (actually it's disabled right now)
- uses [jest-worker](https://github.com/facebook/jest/tree/master/packages/jest-worker) to consume all your CPU cores
- uses [terser](https://github.com/terser-js/terser) without mangling to compress the result 

### TWO bundles to rule the world

- One for "esm"(modern) browsers, which you may load using `type=module`
- Another for an "old"(any non modern) browser, which you may load using `nomodule`

##### Targets for "esm"
 - edge: "16+",
 - firefox: "60+",
 - chrome: "61+",
 - safari: "10.1+",
(2017+)

##### Targets for "ie11"
 - ie: "11-"
 
That's is the oldest living browser, and can be used as a base line.  

## Usage
1. Compile your code to the `esmodules` target with, or without `polyfills`. This is your "base layer".
The modern browser baseline, with 
```js
{
  "presets": [
    ["@babel/preset-env", {
      "targets": {
        "esmodules": true
      },    
      useBuiltIns: "usage",
    }]
  ]
}  
```

2. Fire devolution to produce de-modernized bundles
```bash
// if use have used `useBuiltIns: usage`, thus "esm" polyfills are already inside and could be skipped
yarn devolution ./dist ./dist index.js true

// all the nessesary polyfills would be bundled
yarn devolution ./dist ./dist index.js
```
It will produce `esm` and `ie11` target (nothing more is supported right now) by applying `Babel` one more time
to the already created bundle; then prepending required polyfills.

3. (webpack) setup `public-path`, somewhere close to the script start
```js
__webpack_public_path__ = devolutionBundle + '/';
```
`Parcel` will configure public path automatically.

3.1 (webpack) symlink resources to sub-bundles
 Will be done automatically 


4. Ship the right script to the browser
```js
<script type="module" src="esm/index.js"></script>
<script type="text/javascript" src="ie11/index.js" nomodule></script>
```
### But this approach has issues
IE11 will download both bundles, but execute only the right one.
Use feature detection to pick the right bundle:
```js
      var check = document.createElement('script');
      if (!('noModule' in check)) {
        check.src = "/ie11/index.js"
      } else {
        check.src = "/esm/index.js"
      }
      document.head.appendChild(check);
```

5. Done!

A few seconds to setup, a few seconds to build

##### Drawbacks

- __!!__ doesn't play well with script _prefetching_ - you have to manually specify to prefetch `esm` version,
not the "original" one.
- may duplicate polyfills across the chunks. Not a big deal.

### API
You may file devolution manually
```js
import {devolute} from 'devolution';
await devolute(sourceDist, destDist, mainBundle, polyfillsAreBundled)

// for example

await devolute(
  'dist', // the default webpack output
  'dist', // the same directory could be used as well
  'index.js', // the main bundle (polyfills "base")
  true, // yes - some polyfills (assumed esm) are bundled already,
```