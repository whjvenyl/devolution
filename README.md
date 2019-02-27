Devolution
============
de-evolution gun, as seen in Mario Bros, to help you ship modern, and de-modernized bundles.

## Why?
- ship more modern, more compact and more fast code to 85+% of your customers
- do not worry about transpiling node_modules - use as modern code as you can
- run everywhere
- uses [swc](https://github.com/swc-project/swc) to be a blazing🔥 fast!  

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
1. Compile your code to the `esmodules` target __without__ `polyfills`. This is your "base layer".
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

5. Done!

### API
You may file devolution manually
```js
import {devolute} from 'devolution';
await devolute(sourceDist, destDist, mainBundle, polyfillsAreBundled, targets)

// for example

await devolute(
  'dist', // the default webpack output
  'dist', // the same directory could be used 
  'index.js', // the main bundle (polyfills "base")
  true, // yes - some polyfills (assumed esm) are bundled already,
  {
    oldBrowsers: {ie:9},    
  })
```