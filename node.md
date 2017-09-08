**Node.js**

# Setup

`npm install --save` shortcuts to `npm i -S`. You can install as many packages at once as you like.

## Repo

- [Create and clone repository](https://github.com/new)
- Create **.gitignore** file with `/node_modules`
- `npm init` (`-y` to skip questions)
- `npm i -S nodemon`
- In **package.json** under `scripts`, add `"start": "nodemon"`
- Basic README. Include "Start server with \`npm start\`." 
- Commit

## Express

```
$ npm i -S express
```
```js
// index.js
const express = require('express');
const app = express();

// declare and configure other modules here

app.listen(1000, function() {
	console.log("Listening on port 1000");
});
```

### CSS file

```js
app.use(express.static(__dirname + '/public'));
```
```html
<link rel="stylesheet" href="css/style.css">
```
This will load a CSS file from **/public/css/style.css**.

### Handlebars layout engine

```
$ npm i -S express-handlebars
```

```js
const hbs = require('express-handlebars');
app.engine('handlebars', hbs({ defaultLayout: "main" }));
app.set("view engine", "handlebars");

app.get("/", (req, res) => {
	res.render("index", { myData });
})
```

Create file **views/layouts/handlebars/main.handlebars**. Use Emmet's `!` shortcut to create an HTML template, then put `{{{body}}}` in the `<body>` tag.

Also create **views/index.handlebars** containing your HTML content. `{ myData }` will be passed to it.

### body-parser

For handling form data and enabling middleware

```
$ npm i -S body-parser
```

```js
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }));
```

### Cookies

```
$ npm i -S cookie-parser
```

```js
const cookieParser = require('cookie-parser');
app.use(cookieParser());
```

# Services

Used for persisting and managing data throughout the app.

```js
// services/myService.js
var privateData = "foo";

function getData() {
	return privateData;
}

modules.exports = {
	getData
};
```

```js
const myService = require('./services/myService');
myService.getData();
```