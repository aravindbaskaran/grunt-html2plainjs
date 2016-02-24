Fork of https://github.com/karlgoldstein/grunt-html2js
# grunt-html2plainjs
Converts HTML templates to plain old JavaScript
## Getting Started
This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-html2plainjs --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-html2plainjs');
```

## The "html2plainjs" task

### Overview

Compiles HTML to plain old Javascript. Use `angular: true` option to work in `html2js` mode.

### Setup

By default, this plugin assumes you are following the naming conventions and build pipeline of the [angular-app](https://github.com/angular-app/angular-app) demo application.

In your project's Gruntfile, add a section named `html2plainjs` to the data object passed into `grunt.initConfig()`.

This simplest configuration will assemble all templates in your src tree into a module named `templates-main`, and write the JavaScript source for the module to `tmp/template.js`:

```js
grunt.initConfig({
  html2plainjs: {
    options: {
      // custom options, see below
    },
    main: {
      src: ['src/**/*.tpl.html'],
      dest: 'tmp/templates.js'
    },
  },
})
```


### Gotchas

The `dest` property must be a string.  If it is an array, Grunt will fail when attempting to write the bundle file.

### Options

#### options.base
Type: `String`
Default value: `'src'`

The prefix relative to the project directory that should be stripped from each template path to produce a module identifier for the template.  For example, a template located at `src/projects/projects.tpl.html` would be identified as just `projects/projects.tpl.html`.

#### options.target
Type: `String`
Default value: `'js'`

Language of the output file. Possible values: `'coffee'`, `'js'`.

#### options.module
Type: `String` or `Function`
Default value: `templates-TARGET`

The name of the parent Angular module for each set of templates.  Defaults to the task target prefixed by `templates-`.

The value of this argument can be a string or a function.  The function should expect the module file path and grunt task name as arguments, and it should return the name to use for the parent Angular module.

If no bundle module is desired, set this to false.

#### options.rename
Type: `Function`
Default value: `none`

A function that takes in the module identifier and returns the renamed module identifier to use instead for the template.  For example, a template located at `src/projects/projects.tpl.html` would be identified as `/src/projects/projects.tpl` with a rename function defined as:

```
function (moduleName) {
  return '/' + moduleName.replace('.html', '');
}
```

#### options.quoteChar
Type: `Character`
Default value: `"`

Strings are quoted with double-quotes by default.  However, for projects
that want strict single quote-only usage, you can specify:

```
options: { quoteChar: '\'' }
```

to use single quotes, or any other odd quoting character you want

#### indentString
Type: `String`
Default value: `  `

By default a 2-space indent is used for the generated code. However,
you can specify alternate indenting via:

```
options: { indentString: '    ' }
```

to get, for example, 4-space indents. Same goes for tabs or any other
indent system you want to use.

#### fileHeaderString:
Type: `String`
Default value: ``

If specified, this string  will get written at the top of the output
Template.js file. As an example, jshint directives such as
/* global angular: false */ can be put at the head of the file.

#### fileFooterString:
Type: `String`
Default value: ``

If specified, this string  will get written at the end of the output
file.  May be used in conjunction with `fileHeaderString` to wrap
the output.

#### useStrict:
Type: `Boolean`
Default value: ``

If set true, each module in JavaScript will have 'use strict'; written at the top of the
module.  Useful for global strict jshint settings.

```
options: { useStrict: true }
```

#### htmlmin:
Type: `Object`
Default value: {}

Minifies HTML using [html-minifier](https://github.com/kangax/html-minifier).

```
options: {
  htmlmin: {
    collapseBooleanAttributes: true,
    collapseWhitespace: true,
    removeAttributeQuotes: true,
    removeComments: true,
    removeEmptyAttributes: true,
    removeRedundantAttributes: true,
    removeScriptTypeAttributes: true,
    removeStyleLinkTypeAttributes: true
  }
}
```

#### process:
Type: `Object` or `Boolean` or `Function`
Default value: `false`

Performs arbitrary processing on the template as part of the compilation process.

Option value can be one of:

1. a function that accepts `content` and `filepath` as arguments, and returns the transformed content
2. an object that is passed as the second options argument to `grunt.template.process` (with the file content as the first argument)
3.  `true` to call `grunt.template.process` with the content and no options

#### singleModule
Type: `Boolean`
Default value: `false`

If set to true, will create a single wrapping module with a run block, instead of an individual module for each template file. Requires that the `module` option is not falsy.

#### existingModule
Type: `Boolean`
Default value: `false`

If set to true, will use an existing module with the name from `module`, instead of creating a new module. Requires that `singleModule` is not falsy.

#### watch
Type: `Boolean`
Default value: `false`

If set to true and used in conjunction with a long running/keep-alive process such as grunt-contrib-watch html2plainjs will watch src files for changes and regenerate output to dest. It uses an internal cache so only the file that changes needs to be re-compliled. Useful for development process particularly if you have lots of jade templates. It is very similar to grunt-browserify's watch.

N.B. If using grunt-watch you do not need to run the html2plainjs task again on src changes as it watches internally for these. All you need to do is watch the destination file and live reload on change.

#### amd
Type: `Boolean`
Default value: `false`

If set to true, will wrap output in a define block so it is compatible with AMD module loaders such as RequireJS without requiring you to shim the module.

#### amdPrefixString
Type: `String`
Default value: `define(['angular'], function(angular){\n`

When `options.amd` is set to true, this is what will be prepended to the module to make it compatible with AMD module loaders.  Along with `amdSuffixString`, these two options should allow you to customize the way your AMD module is created.

#### amdSuffixString
Type: `String`
Default Value: `});`

When `options.amd` is set to true, this is what will be postpended to the module to make it compatible with AMD module loaders.  Along with `amdPrefixString`, these two options should allow you to customize the way your AMD module is created.


```
options: {
  jade: {},
  watch: true
}
```

### Jade support

If template filename ends with `.jade` the task will automatically render file's content using [Jade](https://github.com/visionmedia/jade)
then compile into JS.

Options can be passed to Jade within a `jade` property in the plugin options.

```
options: {
  jade: {
    //this prevents auto expansion of empty arguments
    //e.g. "div(ui-view)" becomes "<div ui-view></div>"
    //     instead of "<div ui-view="ui-view"></div>"
    doctype: "html"
  }
}
```

### Usage Examples

See the `Gruntfile.js` in the project source code for various configuration examples.

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).
