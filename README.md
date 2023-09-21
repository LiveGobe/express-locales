# express-locales

Simple express middleware to determine language of request and populate it with locales.

## Installation

This is a [Node.js](https://nodejs.org/en/) module available through the
[npm registry](https://www.npmjs.com/). Installation is done using the
[`npm install` command](https://docs.npmjs.com/getting-started/installing-npm-packages-locally):

```sh
$ npm install express-locales
```

## API

```js
var expressLocales = require('express-locales');

app.use(expressLocales(options));
```

### expressLocales(options)

Creates a middleware with specified options, then synchronously loads languages from `options.supportedLanguages`.
Order for determining language is `query -> cookie -> acceptLanguage -> default`.
Meaning `query` has most priority and `default` has lowest.

```js
app.use(expressLocales({ defaultLanguage:"en", cookieName:"lang", queryName:"lang", supportedLanguages:["en"], directory:"./locales", writeMissing: true }))
```

#### Options

Function accepts these properties in the options object.

##### defaultLanguage

Specifies a language used as default when no cookie or query was found, or as fallback for unsupported languages.

##### cookieName

Specifies a name for cookie holding language name.

##### queryName

Specifies a name for query holding language name.

##### supportedLanguages

Specifies what languages can be used and will be loaded.

##### directory

Specifies a directory to look for locale files.
**Can be full or relative path.**

##### writeMissing

If this option is set to `true` and environment is not `production`, missing locales will be written into separate files.

### req.translate(key, values) and res.locals.translate(key, values)

Retrieves a locale from file specific to language of request.
`key` points to JSON key in locales file, like `foo.bar`.
If `key` is not found, `key` will be returned instead.

```js
app.get("/", (req, res) => {
	res.send(req.translate("foo.bar"));
});

// OR

app.get("/", (req, res) => {
	res.send(req.t("foo.bar"));
});
```

#### Placeholders

`translate` supports placeholders like `{1}` or `{foo}`.
To replace a placeholder in locales file, specify values in `values` object.
If no value is specified, placeholder will remain in string.

```js
// Locales file
// {
//   "foo": "string with {bar} and {0}"
// }

app.get("/", (req, res) => {
	res.send(req.translate("foo", { bar: "bar" }));
});

// string with bar and {0}
```

### req.locale and res.locals.locale

`locales` object contains all locales for language of the request so you can manually access it.

### req.lang and res.locals.lang

`language` property contains language name of request.