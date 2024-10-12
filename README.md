# Serverette — Node.js Testing Web Server

This is a web server as test bed you don't need to install Apache, Express, or other web server to test your HTML/CSS web application front-end.

Note: This server is not for production. Serverette currently support only `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `OPTIONS`, and `PATCH` methods. `CONNECT` and `TRACE` are not supported.

Roadmap: Find reason to add `CONNECT` and `TRACE` methods.


## Installation

```
npm install serverette --save-dev

```


## ES Module VS CommonJS

To know if your project is ES Module type look at the package.json file, it should have `"type": "module",` line.
Your package.json should look something similar to this:

```
{
  "name": "nodejs",
  "type": "module",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@types/node": "^18.0.6"
  },
  "devDependencies": {
    "serverette": "^0.9.0"
  }
}
```


## Setup

Import into your application ES module and create `__dirname`:

```
// import Serverette
import url from "url";
import { serverette } from "serverette";

// get current directory
const __dirname = url.fileURLToPath(new URL(".", import.meta.url));
```

Import into your application in CommonJS (`__dirname` automatically available):
```
// import Serverette
const serverette = require("serverette")
```

You can import both Serverette and callbacks object (ES Module):
```
// import Serverette and callbacks
import { serverette, callbacks } from "serverette"
```

You can import both Serverette and callbacks object (CommonJS:
```
// import Serverette and callbacks
const { serverette, callbacks } = require("serverette")
```

Now you can create your web server with all defaults:
```
// create server "test" directory with test web files:
serverette(__dirname + "/test");
```
Replace `/test` with the name of the test directory. The `__dirname` will return the full path of your working directory.

Important: You need at least one `index.htm` file and must be with `.htm` extension, not `.html`.


## Usage

```
// Serverette acceptable parameters, rootDir is always required
// callbacks is an object of these http types {get, post, put, options, patch}
serverette(rootDir, port, mime, callbacks)
```

- **rootDir (required):** The full working directory can use the built-in `__dirname` variable and must add the directory of the tested files, for example: `__dirname + "/test"`.
- **port** (optional): The default is 8080. Can choose anything you want.
- **mime**  (optional): Built-in MIME according to [mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types). You can add your own MIME type if it is not there. MIME should be an array with objects of mime type and regex for the file extension. Example adding mp3 file type: `serverette(__dirname+"/test", 8080, [{ regex: /\.mp3/, type: "audio/mp3" }])`
- **callbacks {get, post, put, options, patch}** (optional): these are optional custom functions if the default behavior of printing data to the console is not satisfying.


## HTTP Types Callback Behavior

- **GET Callback** (optional): If no callback function provided, it will print the `URL` parameters (search query) data in console (terminal). Note: parameters returned by Serverette are in array pairs like: `[["key","value"],["key","value"],["key","value"]]`
- **HEAD Callback** (optional): It will print `HEAD` with empty array data in console. Note, `HEAD` do not accept body/content in request.
- **POST Callback** (optional): If no callback function provided, it will print the `POST` request data in console.
- **PUT Callback** (optional): If no callback function provided, it will print the `PUT` data in console.
- **DELETE Callback** (optional): Like `HEAD`, `DELETE` do not accept body (content) in request, but it will print DELETE in console with empty array.
- **UPDATE Callback** (optional): Similar to `PUT`, if no callback function provided, it will print the `UPDATE` data in console.

**NOTE:** For all callback above, you can use your custom callback function. Serverette will return an array of the streamed data as a parameter for your callback function like: `yourCallbackFunction(["data stream 1","data stream 2"])` you may need your function to combine or iterate through them.


## Understand Callback Functions

Serverette support callback function to catch the `request` body received and to hijack the `response` before sending it to browser/client. Your custom function/callback will get an array of the data stream as the first parameter and the response object as the second parameter.
If you want to add custom `POST` behavior, you ***need*** to import `callbacks` object first and add your custom `post` function. Note that `post` function is in lowercase.

```
import { serverette, callbacks } from "serverette"

callbacks.post = function(array,response){

	// printing POST body to the console:
    console.log("log array of POST", array)

    // Write response (also can redirect if you want) and close connection
    response.writeHead(200, { 'Content-Type': 'text/html' })
    response.end("POST data received!")
}
serverette(__dirname + "/test",'','',callbacks);

```

## Full Example

```
import url from "url";
import { serverette, callbacks } from "serverette"

// Get current dir
const __dirname = url.fileURLToPath(new URL(".", import.meta.url));

// add custom POST behavior:
callbacks.post = function(array,response){
    console.log("log array of POST", array)
    response.writeHead(200, { 'Content-Type': 'text/html' })
    response.end("POST data received!")
}

// add custom mime:
const myMp3Mime = [{ regex: /\.mp3/, type: "audio/mp3" }]

serverette(__dirname + "/test", 8080, myMp3Mime, callbacks);
```
Terminal output will be something similar to this:
```
Serverette port #: 8080
======================
GET: / MIME: text/html
GET: /w3e.css MIME: text/css
GET: /w3-theme-blue-grey.css MIME: text/css
GET: /h5p-font-awesome.min.css MIME: text/css
GET: /w3e.js MIME: text/javascript
GET: /snow.jpg MIME: image/jpeg
GET: /forest.jpg MIME: image/jpeg
GET: /car.jpg MIME: image/jpeg
GET: /mountains.jpg MIME: image/jpeg
GET: /lights.jpg MIME: image/jpeg
HEAD: []
DELETE: []
PUT: [ '{"test":"testing PUT method"}' ]
PATCH: [ '{"test":"testing PATCH method"}' ]
POST: [ '{"test":"testing POST method"}' ]
OPTIONS: [ '{"test":"testing OPTIONS method"}' ]
=============================
URL(GET) parameters found!
URLSearchParams { 'v' => '4.5.0' }
v 4.5.0
=============================
GET: /fontawesome-webfont.woff2 MIME: font/woff
GET: /fontawesome-webfont.woff2 MIME: font/woff2
GET: /favicon.ico MIME: image/x-icon
POST: [ 'name=Joe&email=Joe%40example.com&subject=Register+me&gender=male' ]

```

## Support, Feedback & Issues

Your contribution is very important, please, visit this link: (https://github.com/emnawer/serverette/issues).

