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

Create file **views/layouts/main.handlebars**. Use Emmet's `!` shortcut to create an HTML template, then put `{{{body}}}` in the `<body>` tag.

Also create **views/index.handlebars** containing your HTML content. `{ myData }` will be passed to it.

### Handling forms

```
$ npm i -S body-parser
```

```js
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }));

app.post('/', (req, res) => {
	const fieldValue = req.body.fieldName;
	res.redirect('back');
});
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

module.exports = {
	getData
};
```

```js
const myService = require('./services/myService');
myService.getData();
```

# Socket.io

## Basic demo

Raise or lower a value and watch it be instantly visible across multiple tabs/browsers - no refresh necessary and no AJAX.

```js
// index.js
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const io = require('socket.io')(server);

var count = 0;

app.get("/", (req, res) => {
	res.sendFile(__dirname + "/index.html");
});

io.on("connection", client => {
	client.emit("new count", count);

	client.on("increment", () => {
		count++;
		io.emit("new count", count);
	});

	client.on("decrement", () => {
		count--;
		io.emit("new count", count);
	});
});

server.listen(3002, function() { // not "app.listen"
	console.log("Listening on port 3002");
});
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
	<script src="/socket.io/socket.io.js"></script>
	<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js"></script>
</head>
<body>
	<h1>Sockets!</h1>

	<h1 id="count"></h1>

	<button id="increment">increment</button>
	<button id="decrement">decrement</button>
</body>

<script>
	var socket = io.connect();
	socket.on('new count', function(count) {
		$('#count').html(count);
	});
	$('#increment').click(function() {
		socket.emit('increment');
	});
	$('#decrement').click(function() {
		socket.emit('decrement');
	});
</script>
</html>

```

## URL parameters

```js
// client.js
var socket = io.connect({query: "foo=bar"});
```
```js
// index.js
io.on('connect', (socket) => {
	var query = socket.handshake.query;
})
```
