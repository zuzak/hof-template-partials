# HOF-template-partials

Home Office Forms template partials is a collection of mustache partials commonly used in HOF applications.  It also contains a collection of i18n translations used within the template partials. All contents are designed to be extended in your individual applications.

## installation

```bash
$ npm install --save hof-template-partials
```

## Usage

### Template partials

#### Standalone

Template partials can be used by adding the route to the views directory to your express application views setting. You will need to be using the HTML view engine with Hogan and Mustache.

```js
var app = require('express')();

app.set('view engine', 'html');
app.set('views', [
  // your application shared views
  path.resolve(__dirname, './path/to/views'),
  // the module exports paths to views and translations directories
  require('hof-template-partials').views
]);
```

The views are now available when calling `res.render('view-name')` from express.

#### HOF Application

When used in a hof application in conjunction with [express-partial-templates](https://github.com/UKHomeOffice/express-partial-templates) the contents of the views directory are added to `res.locals.partials`. These are added right to left so conflicting views are resolved from the left-most directory.

```js
var app = require('express')();

app.set('view engine', 'html');
app.set('views', [
  path.resolve(__dirname, './path/to/views'),
  require('hof-template-partials').views
]);
app.use(require('express-partial-templates')(app));

app.use(function (req, res, next) {
  // res.locals.partials contains all views from the views dir in this repo
  // which are extended by any local views in ./path/to/views
  next();
});
```

### Translations

The provided translations are designed to be used in conjunction with a translations library such as [i18n-future](https://github.com/lennym/i18n-future).

The exported `resources` method will return a compiled object containing the translations, which can be passed to an `i18n` instance as a pre-compiled resource.

```js
const translate = require('i18n-future').middleware({
  resources: require('hof-template-partials').resources()
});
app.use(translate);
```

By default the namespace for this translation is `default`. A custom namespace can be specified by passing it as an argument to the `resources` function.

```js
const translate = require('i18n-future').middleware({
  resources: require('hof-template-partials').resources('hof-common'),
  fallbackNamespace: 'hof-common'
});
app.use(translate);
```