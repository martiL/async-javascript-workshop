# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      //TODO
      if (err) return reject(err);
      resolve(data);
    });
  });
}
readFile("./files/demofile.txt", "utf-8").then(
  data => console.log(data),
  err => console.log(err)
);
```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) reject(err);
      resolve(result);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("./files/demofile.txt", "utf-8")
  .then(data => {
    return zlibPromise(data);
  })
  .then(result => console.log(result))
  .catch(err => console.log("Failed to load", err)); // --> Load it then zip it and then print it to screen
```

# Question 3

Convert the previous code so that it now chains the promise as well.

# Question 4

Convert the previous code so that it now handles errors using the catch handler

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function timeOut(sleep) {
  return new Promise(reject =>
    setTimeout(reject, sleep, "Reading the file takes more than 1 second.")
  );
}

function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep, "file was read"));
} // This resolves a promise after 5 seconds, pretend it's a large file being read from disk

Promise.race([timeOut(1000), readFileFake(5000)])
  .then(val => console.log(val))
  .catch(val => console.log(val));
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

function safePublish() {
  return publish().then(val => (val.status === 403 ? authenticate() : val));
}

Promise.race([safePublish(), timeout(5000)])
  .then(val => {
    return console.log("published");
  })
  .then(val => console.log(val))
  .catch(val => console.log(val));
```
