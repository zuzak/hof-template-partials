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

#### Standalone

The provided translations are designed to be used in conjunction with a translations library such as [i18n-future](https://github.com/lennym/i18n-future). The source files are compiled automatically post-install. If you need to re-compile run the following:

```bash
$ npm run transpile
```

The compiled translations file can be found at `hof-template-partials/translations/{lang}/default.json.`

To use with i18n-future:

app.js
```js
var i18n = require('i18n-future')({
  path: require('hof-template-partials').translations
});

i18n.on('ready', function () {
  var lookedup = i18n.translate('key');
});
```

#### HOF Application

The provided translations can be combined with your application shared and route specific translations by using [hof-transpiler](https://github.com/UKHomeOffice/hof-transpiler). You can run the following script:

```bash
$ npm install hof-transpiler
$ ./node_modules/.bin/hof-transpiler path/to/translations/src -w --shared path/to/shared/translations/src --shared node_modules/hof-template-partials/translations/src
```

or add the following to `scripts` in `package.json`
```json
"scripts": {
  "transpile": "hof-transpiler path/to/translations/src -w --shared path/to/shared/translations/src --shared node_modules/hof-template-partials/translations/src"
}
```

and run with:

```bash
$ npm run transpile
```

This will extend from right to left so any duplicate keys will be overwritten by the left-most source.

### Rendering Terms & Conditions and Cookies

These templates are scoped to their respective translation files so you would render them in the following way:

app.js
```js
app.get('/terms', function (req, res, next) {
  i18n.on('ready', function () {
    // express will look for a terms.html template in the
    // directories defined earlier
    res.render('terms', i18n.translate('terms'))
  });
});
```
