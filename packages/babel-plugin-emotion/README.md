# babel-plugin-emotion

> Babel plugin for the minification and optimization of [emotion](https://github.com/emotion-js/emotion) styles.

The Babel Plugin is highly recommended, but not required in version 8 and above.

## Feature table

| Feature/Syntax           | Native | Babel Plugin Required | Notes                                                                                                                                        |
|--------------------------|:------:|:---------------------:|----------------------------------------------------------------------------------------------------------------------------------------------|
| css``               |    ✅   |                       |                                                                                                                                              |
| `css(...)`               |    ✅   |                       | Generally used for object styles.                                                                                                            |
| styled('div')`` syntax |    ✅   |                       | Both string and object styles work without this plugin.                                                                                      |
| styled.div`` syntax    |        |           ✅           | Supporting the shortcut syntax without the Babel plugin requires a large list of valid elements to be included in the bundle.                |
| Minification             |        |           ✅           | Any leading/trailing space between properties in your `css` and `styled` blocks is removed. This can reduce the size of your final bundle.   |
| Dead Code Elimination    |        |           ✅           | Uglifyjs will use the injected `/*#__PURE__*/` flag comments to mark your `css` and `styled` blocks as candidates for dead code elimination. |
| Static Extraction        |        |           ✅           | Generated CSS that is eligible for extraction can be moved to an external css file.                                                          |
| Source Maps              |        |           ✅           | When enabled, navigate directly to the style declaration in your javascript file.                                                            |
| `css` as Prop            |        |           ✅           | Convenient helper for calling `css` and appending the generated className during compile time.                                               |

## Example

**In**

```javascript
const myStyles = css`
  font-size: 20px;
  @media(min-width: 420px) {
    color: blue;
    ${css`width: 96px; height: 96px;`};
    line-height: 26px;
  }
  background: green;
  ${{ backgroundColor: "hotpink" }};
`
```

**Out**

```javascript
const myStyles = /* #__PURE__ */css(
  'font-size:20px;@media(min-width:420px){color:blue;',
  /* #__PURE__ */ css('width:96px;height:96px;'),
  ';line-height:26px;}background:green;',
  { backgroundColor: 'hotpink' },
  ';'
)
```

## Installation

```sh
npm install --save-dev babel-plugin-emotion
```

## Usage

### Via `.babelrc` (Recommended)

**.babelrc**

Without options:

```json
{
  "plugins": ["emotion"]
}
```

With options:

*Defaults Shown*

```json
{
  "plugins": [
    ["emotion", { 
      "hoist": false,
      "sourceMap": false,
      "extractStatic": false,
      "importedNames": {
        "styled": "styled",
        "css": "css",
        "keyframes": "keyframes",
        "injectGlobal": "injectGlobal",
        "fontFace": "fontFace",
        "merge": "merge"
      }
    }]
  ]
}
```

Recommended Setup 

Use [Babel's `env` property](https://babeljs.io/docs/usage/babelrc/#env-option)

**.babelrc**
```json
{
  "env": {
    "production": {
      "plugins": [["emotion", { "sourceMap": false, "hoist": true }]]
    },
    "development": {
      "plugins": [["emotion", { "sourceMap": true, "hoist": false  }]]
    }
  }
}
```

### Via CLI

```sh
babel --plugins babel-plugin-emotion script.js
```

### Via Node API

```javascript
require("@babel/core").transform("code", {
  plugins: ["babel-plugin-emotion"]
});
```

## Options

### `hoist`

`boolean`, defaults to `false`.

This option enables the following:

 - Any argument supplied to `css` or `styled` is hoisted. 

By hoisting the argument, or assigning the value to a variable, 
emotion is able to leverage the use of a [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
[cache](https://github.com/emotion-js/emotion/blob/6257f0c9cb00db9cbd08a9d6995f335730808329/packages/emotion/src/index.js#L85-L116) to increase performance. Users of object styles will benefit the most from enabling this option.  

**In**

```javascript
css({ color: 'brown' });
```

**Out**

```javascript
var _ref = { color: 'brown' };
css(_ref);
```


### `sourceMap`

`boolean`, defaults to `false`.

This option enables the following:

 - Injected source maps for use in browser dev tools

[**Documentation**](docs/source-maps.md)

### `extractStatic`

`boolean`, defaults to

This option enables the following:

 - Extract static styles into CSS files.

[**Documentation**](docs/extract-static.md)

### `importedNames` 


`object`, defaults to

```json
{
  "styled": "styled",
  "css": "css",
  "keyframes": "keyframes",
  "injectGlobal": "injectGlobal",
  "fontFace": "fontFace",
  "merge": "merge"
}
```

This option enables the following:

 - Configurable import names
 
[**Documentation**](docs/configurable-imports.md)

