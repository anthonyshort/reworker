reworker
========

Simple way to use Rework with a Grunt-like config

* Easily rework your CSS using a CLI
* Use a Grunt-like config to add middleware to Rework
* Built-in support for minifying CSS
* Supports css-whitespace
* Build variants for various browsers (usually just oldIE)

## Get it

```
npm install -g reworker
```

## Usage

Reworker creates a Rework instance for you from stdin, passes this to a config, and then writes to stdout. It also
helps by having built-in support for minifying CSS. It's sort of like a `Gruntfile.js`.

Let's say you have a `styles.css` file. Add a `rework.js` file next to it:

```js
module.exports = function(rework) {
  return rework;
};
```

Then run reworker:

```
reworker < styles.css > build.css
```

By default, this config isn't doing much. But now you can load up some Rework
plugins in that config and `rework.use` them. You have access to all of the 
built-in Rework plugins, like `references`, `inline`, `mixin` etc.

## Variants

You can pass a variant to reworker:

```
reworker -v ie < style.css
```

This can be accessed via `rework.variant` from within the config. This lets
you change the middleware options that are used based on any variant so you
can have multiple builds. This could include mobile versions, retina versions etc.

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
