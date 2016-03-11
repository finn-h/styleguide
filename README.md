# Front-End Styleguide
Front-end development styleguide with Sass, JavaScript and Handlebars.
Uses the [Gulp](http://gulpjs.com/) task runner to compile [Sass](http://sass-lang.com/) files, [lint JavaScript](http://jshint.com/) and create static HTML files from [Handlebars](http://handlebarsjs.com/) files.


## Contents
1. [Dependencies](#dependencies)
2. [Installation](#installation)
3. [Usage](#usage)
  1. [Tasks](#tasks)
  2. [Sass](#sass)
  3. [JavaScript](#javascript)
  4. [Handlebars](#handlebars)
  5. [Assets](#assets)


## Dependencies
* [Node.js with npm](https://nodejs.org/)
* Use [Node.js 5.x](https://nodejs.org/en/download/stable/) for Windows.


## Installation
1. Get the [latest release](https://github.com/MVSde/styleguide/releases/latest).
2. Run `npm install` to download Node Modules.
3. To update previously downloaded Node Modules run `npm update`.
4. Install global Gulp with `npm install -g gulp`. This step is required to get CLI access to gulp.


## Usage

### Tasks
These are the main Gulp tasks:
* Run `gulp` to start the default task. This task watches for file changes. All generated files are placed in `dev`.
* Run `gulp development` to start the default task without file watching. All generated files are placed in `dev`.
* Run `gulp production` to create prduction ready resources. This task minifys CSS and JavaScript files and compresses images. All genereated files will be placed in `dist`.

There are more tasks available for standalone execution:
* `sass:dev` and `sass:dist` for Sass compilation.
* `js:dev` and `js:dist` for JavaScript concatenation and linting.
* `handlebars:dev` and `handlebars:dist` for static HTML file generation.
* `assets:dev` and `assets:dist` for assets copying and image minification.

The generated folders `dev` and `dist` are not included with Git.


### Sass
Located in `src/css`.<br>
Output to `dev/css` or `dist/css`.

*Sass* is a CSS-Preprocessor supporting variables, nesting and mixins - among many other features.
For a complete documentation jump to the [Sass Basics](http://sass-lang.com/guide).

This styleguide splits the CSS into small parts. This ensures a far better organization of style declarations. Each component sits in it's own file and is re-usable across the project. See [Handlebars-Section](#handlebars) for the HTML-side of componentization.

The function `@import` includes the corresponding file in the main Sass file. The final output is one large CSS file to minimize requests. See `src/css/main.scss` for more information.

The development task generates sourcemaps.

Internet Explorer versions 6 to 9 have a limit on the amount of selectors used in one stylesheet. If this limit is reached, [Bless](http://blesscss.com/) generates an Internet Explorer specific splitted stylesheet. This does not affect any other browser. Since Microsoft discontinued these old Internet Explorer version, this feature is likely to be removed in a future version of the Styleguide.


### JavaScript
Located in `src/js`.<br>
Output to `dev/js` or `dist/js`.

The Gulp plugin [gulp-include](https://github.com/wiledal/gulp-include) works similar to the `@import` function used by Sass.

With the syntax `//=include relative/path/to/file.js` smaller JavaScript components can be included into the *main.js*. The path may contain globbing for example `//=include components/**/*.js`.

`require` only includes once. This helps to batch-include multiple files but with specific files first or last:

```js
//=require libraries/jquery.js
//=require libraries/*.js
```

The development task runs JSHint and generates sourcemaps.
The production task uglifies the source.

jQuery 2 is included with this styleguide but it can be easily replaced with another library or removed completely. All 3rd-party stuff should be placed inside the folder `src/js/libraries`. JSHint ignores files in this folder to prevent error spamming.


### Handlebars
Located in `src/html`.<br>
Output to `dev` or `dist`.

*Handlebars* is an HTML templating engine based on JavaScript. Gulp creates static HTML files from Handlebars files.

Hierarchic organisation with subfolders is supported. The output reflects the input file tree.

#### Layouts
Layouts determine the overall structure of the HTML document. They contain the `<head>` area, scripts and styles.

Layouts are located in `src/html/partials/layouts`.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>{{file.meta.title}}</title>
  </head>
  <body>
    {{{block "content"}}}
  </body>
</html>
```

#### Includes
These files can be included in all other files (even in other includes). The process is comparable to PHP's `include`.

Includes are located in `src/html/partials/includes`. The Handlebars renderer accesses them relative to the `src/html/partials` folder.

The syntax `{{> "includes/example" }}` includes the file `src/html/partials/includes/example.hbs`.

#### Pages and Components
Pages are the actual web pages displayed by the browser.<br>
Components are reference pages for smaller UI parts. The gulp task `production` will not render them.

The `default` gulp task injects a flyout menu into pages and components for easier navigation during development.

Pages are located in `src/html/pages`, components in `src/html/components`.

The YAML front matter between `---` contains general information like the page title and description. This will be used primarily by layouts. You can add additional information, which can be accessed via `{{file.meta.key}}`. Replace `key` with the name from the front matter.

```html
---
title: Example
description: Only used for components.
---
{{#extend "layouts/components"}}
  {{#content "content"}}
    <p>This will be injected into the layout.</p>
  {{/content}}
{{/extend}}
```

#### Styleguide CSS
The components pages and the development menu use some styling.

All styles are located in `src/html/css/sg.scss`.

The style definitions located in `src/css` use the prefix `sg-` to ensure compatibility with the main stylesheet.

### Assets
Located in `src/assets`.<br>
Output to `dev/assets` or `dist/assets`.

All files and folders placed in `src/assets` will be copied to `dev/assets` or `dist/assets`. The distribution task minifies images with a lossless compressor.
