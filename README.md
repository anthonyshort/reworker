reworker
========

Simple way to use Rework with a Grunt-like config

```
npm install -g reworker
```

* Easily rework your CSS using a CLI
* Use a Grunt-like config to add middleware to Rework
* Built-in support for minifying CSS
* Supports css-whitespace
* Build variants for various browsers (usually just oldIE)

## Usage

Add a `rework.js` file to your CSS project:

```js
module.exports = function(rework) {
  return rework;
};
```

Then run reworker:

```
reworker < styles.css > build.css
```

Reworker takes input via stdin and outputs via stdout. 

By default, this config isn't doing much, but it is passed a built Rework instances
ready for you to you.

## Options

Run `reworker -h` for a list of options

## Example Usage

```js
var autoprefixer = require('autoprefixer'),
    vars = require('rework-variables'),
    inherit = require('rework-inherit');

module.exports = function(rework) {

  // Could make this a module and access it with client-side JS too
  var variables = {
    'base': 16,
    'baseline': 21,
    'baseFontSize': '16px',
    'baseLineHeight': '21px'
  };

  // Replace variables in the CSS
  rework.use(vars(variables));

  // You could put this in its own module
  // and access it with your normal JS too.
  var breakpoints = {
    '(min-width: 20rem)': {
      name: 'mobile',
      columns: 4,
      spacing: ['0', '10px', '20px', '40px']
    }
  };

  // Declare mixins
  // These are simple properties that are replaced
  // with a bunch of other properties. Just return them
  // from the function.
  rework.use(rework.mixin({
    'border-top-radius': function(val) {
      return {
        'border-top-left-radius': val,
        'border-top-right-radius': val
      };
    },
    'border-bottom-radius': function(val) {
      return {
        'border-bottom-left-radius': val,
        'border-bottom-right-radius': val
      };
    }
  }));

  // Declare functions
  rework.use(rework.function({
    'baseline': function(n) {
      return (n * variables.baseline) + 'px';
    }
  }));

  // Allow inlining of images using inline()
  rework.use(rework.inline('./'));

  // Extra easing functions
  rework.use(rework.ease());

  // rgba(hex, n)
  rework.use(rework.colors());

  // Reference other properties from within a selector
  rework.use(rework.references());

  // Retina background images
  if(options.variant !== 'ie8') {
    rework.use(rework.at2x());
  }

  // Auto-prefix properties and values
  if(options.variant === 'ie8') {
    rework.use(autoprefixer.rework(["ie 8"]));
  }
  else {
    rework.use(autoprefixer.rework(["last 2 versions"]));
  }

  // Allow selector extending. This comes at the very
  // end so that we can extend any of the classes that
  // have been generated above
  rework.use(inherit({
    propertyRegExp: /^extend$/
  }));

  return rework;
};
```
