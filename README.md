<img src="http://rawgit.com/caiogondim/pipeline.js/master/img/icon.svg">

# pipeline.js

<img src="http://travis-ci.org/caiogondim/pipeline.js.svg?branch=master" alt="Travis CI"> [![codecov](https://codecov.io/gh/caiogondim/obstructed.js/branch/master/graph/badge.svg)](https://codecov.io/gh/caiogondim/obstructed.js)


Pipeline works like the pipe operator more common in functional programming
languages, like Elixir.

This lib supports **sync** and **async** arguments.

If all arguments are sync functions or literal, the pipeline will run as a normal
function returning the last value computed. In case one of the arguments is a
function that returns a promise or a promise instance (then-able object), a
promise will be returned at the end.

## Usage

### Sync

```js
function double(x) {
  return x * 2
}

function square(x) {
  return x * x
}

var output = pipeline(
  2,
  double,
  square
)
console.log(output) // => 16
```

### Async

```js
pipeline(
  bookingDetails.userId,
  fetchUserById, // async
  JSON.parse
)
  .then(function(result) {
    console.log(result)
  })
  .catch(function(error) {
    console.log(error)
  })
```

### Mixed

It is also possible to mix sync and async arguments.
Whenever a function that generates promises or a promise intance is found,
the lib switchs to async mode and will return a `Promise`.

## Installation

To use the library, install it through npm

```bash
npm install @caiogondim/pipeline --save
```

To port it to Browser or any other (non CJS) environment, use your favorite CJS
bundler. No favorite yet? Try: [Browserify](http://browserify.org/),
[Webmake](https://github.com/medikoo/modules-webmake) or
[Webpack](http://webpack.github.io/).

## Examples

### Validation (sync and async)

```js
// Before
function validateEmail(email) {
  email = trim(email)
  validateEmailTld(email)
  validateEmailFormat(email)
}

// After
function validateEmail(email) {
  return pipeline(
    email,
    trim,
    validateEmailTld,
    validateEmailFormat
  )
}

try {
  validateEmail('john@email.com')
} catch (error) {
  console.log(error)
}
```

### Straight-forward Cases

```js
// Before
if (cache && localStorage.getItem(endpoint)) {
  return m.prop( JSON.parse( localStorage.get(endpoint) ) )
}

// After
if (cache && localStorage.getItem(endpoint)) {
  return pipeline(
    localStorage.get(endpoint),
    JSON.parse,
    m.prop
  )
}
```

### Usage with const

```js
// Before
let items = base64ToJSON(response.data.content)
items = Array.isArray(items) ? items : [items]

// After
const items = pipeline(
  base64ToJSON(response.data.content),
  x => Array.isArray(x) ? x : [x]
)
```

### Functional Updates

```js
// Before
return Event.create(
  Object.assign(attrs, { parent_id: parentId, status: 'draft' })
)

// After
return pipeline(
  Object.assign(attrs, { parent_id: parentId, status: 'draft' }),
  Event.create
)
```

## Reference and credits
- [Ramda.js pipe](http://ramdajs.com/docs/#pipe)
- [lodash.js flow](https://lodash.com/docs#flow)
- [pipe.py](https://github.com/JulienPalard/Pipe)
- Some exampes taken from https://github.com/mindeavor/es-pipeline-operator/wiki/Example-Use-Cases

---

[caiogondim.com](https://caiogondim.com) &nbsp;&middot;&nbsp;
GitHub [@caiogondim](https://github.com/caiogondim) &nbsp;&middot;&nbsp;
Twitter [@caio_gondim](https://twitter.com/caio_gondim)
