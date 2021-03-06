# take-a-rest

Simple tutorial for your first [REST API](https://www.wikiwand.com/en/Representational_state_transfer).
And then, just take a rest.

## Introduction

In this tutorial you will learn how to start a [HTTP server](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_web_server), parse URL parameters (and queries), use different [HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) and prepare very simple [REST API](https://www.wikiwand.com/en/Representational_state_transfer) linked with even simpler database.
One step at a time.
Each presented example tries to answer one question:

* **01_HttpServer** - _How to start a HTTP server?_
* **02_UrlParameters** - _How to extract URL parameters (and queries) from HTTP requests?_
* **03_HttpMethods** - _How to use different HTTP methods (e.g. GET, PUT, DELETE)?_
* **04_RestDatabase** - _How to prepare a simple REST API integrated with even simpler database?_
* **05_RestSQL** - _How to prepare a simple REST API integrated with a slightly harder-to-use SQL database?_

### Infrastructure

This tutorial is build over [node.js](https://nodejs.org/en/) infrastructure with a help of two packages:

* [Express](http://expressjs.com) - a minimal web framework (for _building http server_, _handling request/response_)
* [lowdb](https://github.com/typicode/lowdb) - single JSON file database (for _data access and storage_)

For project installation and management we will use [NPM Package Manager](https://www.npmjs.com/). For testing servers and REST APIs we will use [cURL](https://curl.haxx.se/).

### Installation

* Download and install [node.js](https://nodejs.org/en/download/)
* Check if these commands work in console: `node --version` and `npm --version`
* Clone this repository `git clone https://github.com/quepas/take-a-rest`
* Go to the project directory `cd take-a-rest`
* Install required packages using NPM command `npm install`
* Run example code using `npm run 01_HttpServer`
* **FOR TESTING**: Check if you have [cURL](https://curl.haxx.se/), otherwise install it

### Running example

In order to run an example type in console `npm run` with example name.
For instance `npm run 01_HttpServer`.
After that, an appropriate HTTP server will start and wait for your requests.

For simple interaction by GET method you can use a browser.
Just type server URL (usually `localhost:3000`).
For complex interaction with server using other HTTP methods use _cURL_ utility from your console.
To specify a HTTP method use option `-X` as follow:

```bash
curl -X GET "localhost:3000"
```

```bash
curl -X PUT "localhost:3000/item/bicycle"
```

```bash
curl -X DELETE "localhost:3000/item/bicycle"
```

### Possible errors

* **Address In Use** [`Error: listen EADDRINUSE :::3000`] - another server or service is using port 3000. Close this process (might be hidden) or use different port number for your server.

## Tutorial

### 01_HttpServer

The first example shows how to import _Express_ framework and create HTTP web server with it.
The server listens for requests on port `3000`.
The port number can be change to any valid, not used port.
This piece of code handles two requests using GET method:

* Show main page (URL: `/`)
* Show _hello_ subpage (URL: `/hello`).

Each incoming request is printed out using `printReqSummary` function.
This method will help us until the end of the tutorial.

### 02_UrlParameters

The second example extends previous HTTP server with extraction of parameters and queries from request URLs.
Parameters belong to the URL path, thus they need to be marked in the URL pattern (in _Express_ we use colon to mark a parameter in URL e.g. `/hello/:name`).
This pattern tells us to handle any URL beginning with `/hello/`, followed by a single part of a path.

The queries represents additional data passed in a URL.
They are always at the end of the URL path after `?` sign.
A single query consists of a key and a value.
Many queries can be chained with `&` sign.
For example: `/patient?name=John&surname=Doe`.

### 03_HttpMethods

In this example we are going beyond GET method (_check table below_).
The server holds a list of item names.
Using PUT method and URL `/item/:name` we can add a new item to a list.
But adding repeatedly the same item will have no effects except for the first time.
This is a well-known property called [idempotency](http://www.restapitutorial.com/lessons/idempotency.html).
Current list of items is visible through `GET /item` request.
In order to remove an item with given name, use `DELETE /item/:name` request.

| HTTP Method | Description             |
| ----------- | ----------------------- |
| GET         | Get resource            |
| POST        | Submit new resource     |
| PUT         | Add or replace resource |
| DELETE      | Delete resource         |

It is worth to notice that handling different HTTP methods requires only a change of _Express_ method, for example from `app.get()` to `app.put()`.
But having an excepted behaviour of a request (`DELETE` method removes resource at given URL, [idempotence](http://www.restapitutorial.com/lessons/idempotency.html) property etc.) depends solely on a programmer a.k.a you.

### 04_RestDatabase

The last example combines all previous knowledge.
After the start of the server, an empty database in `db.json` file is created.
Using a whole range of requests, at our disposal, we can populate database with patients (`POST`), display all patients or one (`GET`), erase (`DELETE`) and modify (`PUT`) them.
Each request is followed by a response with an appropriate HTTP response status code:

| Response Code | Name        | Description                      |
| ------------: | ----------- | -------------------------------- |
|           200 | OK          | Request has succeeded            |
|           400 | Bad request | Invalid syntax of a request      |
|           404 | Not found   | Requested resource was not found |

It is worth to notice that the same URL might have different meaning in different usage context. For example a request with URL `/patient/:id` will:

* Return patient with an `id` if we use GET method
* Remove patient with an `id` if we use DELETE method
* Update patient with an `id` if we use PUT method

This is similar to having a single reference (URL) and passing it to different functions (HTTP methods).
This approach allows to think about the resources and their placement first.

### 05_RestSQL

This example is all about upgrading!
We will switch from a single-file database `lowdb` to a full-fledge relational SQL database, namely, [MySQL](https://www.mysql.com/).

First, install [MySQL](https://www.mysql.com/) and prepare our database.
On Ubuntu, the procedure is as simple as running this command: `sudo apt-get install mysql-server`.
Then, login to the database shell `mysql -u user -p` where `user` could be `root` or any other username defined by you.
In the shell, create our database `CREATE DATABASE rest_db;` select it `USE rest_db;`, and finally, create table for our patients in the database:

 ```sql
 CREATE TABLE patients(id INTEGER NOT NULL AUTO_INCREMENT, name VARCHAR(255), surname VARCHAR(255), PRIMARY KEY(id));
 ```

We are all set.
Let's move to our script `app.js`.
However, before running the script, set up correctly username and password to the database, here (lines `6`-`12`):

```js
var db = mysql.createConnection({
  host: "localhost",
  user: "your_username",
  password: "your_password",
  port: 3306,
  database: 'rest_db'
});
```

The overall structure of the script is the same as from the previous `04_RestDatabase` script.
Go over each REST request and analyse what has changed.
Do the underlying principles of the REST service stay the same?

### Future extensions

Replace [lowdb](https://github.com/typicode/lowdb) with your favourite database (e.g. [PostgreSQL](https://www.postgresql.org/), [MySQL](https://www.mysql.com/), [MongoDB](https://www.mongodb.com/)). Read more about [Express + databases integration](http://expressjs.com/en/guide/database-integration.html)

## Additional materials

### Programming

* [Express + Database Integration](http://expressjs.com/en/guide/database-integration.html) (MySQL, PostgreSQL, SQLite and many others)
* [Express API Documentation](http://expressjs.com/en/4x/api.html)
* [Node.js Tutorial](https://www.tutorialspoint.com/nodejs/index.htm)
* [Lowdb documentation](https://github.com/typicode/lowdb/blob/master/README.md)

### Hypertext Transfer Protocol (HTTP)

* [What is a web (HTTP) server?](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_web_server)
* [An Overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
* [HTTP Request Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
* [HTTP Response Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

### Representational state transfer (REST)

* [What exactly is "restful programming"?](https://stackoverflow.com/questions/671118/what-exactly-is-restful-programming)
* [REST API Tutorial](http://www.restapitutorial.com/)
