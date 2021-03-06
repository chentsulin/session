# koa-session

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][coveralls-image]][coveralls-url]
[![Gittip][gittip-image]][gittip-url]
[![David deps][david-image]][david-url]
[![iojs version][iojs-image]][iojs-url]
[![node version][node-image]][node-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/koa-session.svg?style=flat-square
[npm-url]: https://npmjs.org/package/koa-session
[travis-image]: https://img.shields.io/travis/koajs/session.svg?style=flat-square
[travis-url]: https://travis-ci.org/koajs/session
[coveralls-image]: https://img.shields.io/coveralls/koajs/session.svg?style=flat-square
[coveralls-url]: https://coveralls.io/r/koajs/session?branch=master
[gittip-image]: https://img.shields.io/gittip/fengmk2.svg?style=flat-square
[gittip-url]: https://www.gittip.com/fengmk2/
[david-image]: https://img.shields.io/david/koajs/session.svg?style=flat-square
[david-url]: https://david-dm.org/koajs/session
[iojs-image]: https://img.shields.io/badge/io.js-%3E=_1.0-yellow.svg?style=flat-square
[iojs-url]: http://iojs.org/
[node-image]: https://img.shields.io/badge/node.js-%3E=_0.12-green.svg?style=flat-square
[node-url]: http://nodejs.org/download/
[download-image]: https://img.shields.io/npm/dm/koa-session.svg?style=flat-square
[download-url]: https://npmjs.org/package/koa-session

 Simple cookie-based session middleware for Koa.

## Installation

```js
$ npm install koa-session
```

## Example

  View counter example:

```js
var session = require('koa-session');
var koa = require('koa');
var app = koa();

app.keys = ['some secret hurr'];

var CONFIG = {
  key: 'koa:sess', /** (string) cookie key (default is koa:sess) */
  maxAge: 86400000, /** (number) maxAge in ms (default is 1 days) */
  overwrite: true, /** (boolean) can overwrite or not (default true) */
  httpOnly: true, /** (boolean) httpOnly or not (default true) */
  signed: true, /** (boolean) signed or not (default true) */
};
app.use(session(CONFIG, app));
// or if you prefer all default config, just use => app.use(session(app));

app.use(function *(){
  // ignore favicon
  if (this.path === '/favicon.ico') return;

  var n = this.session.views || 0;
  this.session.views = ++n;
  this.body = n + ' views';
})

app.listen(3000);
console.log('listening on port 3000');
```
For Koa 2, use [koa-convert](https://github.com/gyson/koa-convert) to convert the session middleware :

```js
const koa = require('koa');
const session = require('koa-session')
const convert = require('koa-convert');

const app = new koa();
app.use(convert(session(app)));

// codes
```

## API

### Options

  The cookie name is controlled by the `key` option, which defaults
  to "koa:sess". All other options are passed to `ctx.cookies.get()` and
  `ctx.cookies.set()` allowing you to control security, domain, path,
  and signing among other settings.

#### Custom `encode/decode` Support

  Use `options.encode` and `options.decode` to customize your own encode/decode methods.

### Hooks

  - `valid()`: valid session value before use it
  - `beforeSave()`: hook before save session

### External Session Stores

  Session will store in cookie by default, but it has some disadvantages:

  - Session stored in client side unencrypted.
  - [Browser cookie always have length limit](http://browsercookielimits.squawky.net/).


  You can store the session content in external stores(redis, mongodb or other DBs) by pass `options.store` with three methods(need to be generator function or async function):

  - `get(key)`: get session object by key
  - `set(key, sess, maxAge)`: set session object for key, with a `maxAge` (in ms)
  - `destroy(key)`: destroy session for key


  Once you passed `options.store`, session is strong dependent on your external store, you can't access session if your external store is down. **Use external session stores only if necessary, avoid use session as a cache, keep session lean and stored by cookie!**

### Session#isNew

  Returns __true__ if the session is new.

```js
if (this.session.isNew) {
  // user has not logged in
} else {
  // user has already logged in
}
```

### Session#maxAge

  Get cookie's maxAge.

### Session#maxAge=

  Set cookie's maxAge.

### Destroying a session

  To destroy a session simply set it to `null`:

```js
this.session = null;
```

## License

  MIT
