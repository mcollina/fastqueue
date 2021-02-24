# fastq

![ci][ci-url]
[![npm version][npm-badge]][npm-url]
[![Dependency Status][david-badge]][david-url]

Fast, in memory work queue.

Benchmarks (1 million tasks):

* setImmediate: 812ms
* fastq: 854ms
* async.queue: 1298ms
* neoAsync.queue: 1249ms

Obtained on node 12.16.1, on a dedicated server.

If you need zero-overhead series function call, check out
[fastseries](http://npm.im/fastseries). For zero-overhead parallel
function call, check out [fastparallel](http://npm.im/fastparallel).

[![js-standard-style](https://raw.githubusercontent.com/feross/standard/master/badge.png)](https://github.com/feross/standard)

  * <a href="#install">Installation</a>
  * <a href="#usage">Usage</a>
  * <a href="#api">API</a>
  * <a href="#license">Licence &amp; copyright</a>

## Install

`npm i fastq --save`

## Usage

```js
'use strict'

var queue = require('fastq')(worker, 1)

queue.push(42, function (err, result) {
  if (err) { throw err }
  console.log('the result is', result)
})

function worker (arg, cb) {
  cb(null, 42 * 2)
}
```

or

```js
var queue = require('fastq').promise(worker, 1)

async function worker (arg) {
  return 42 * 2
}

async function run () {
  const result = await queue.push(42)
  console.log('the result is', result)
})
}

run()
```

### Setting this

```js
'use strict'

var that = { hello: 'world' }
var queue = require('fastq')(that, worker, 1)

queue.push(42, function (err, result) {
  if (err) { throw err }
  console.log(this)
  console.log('the result is', result)
})

function worker (arg, cb) {
  console.log(this)
  cb(null, 42 * 2)
}
```

## API

* <a href="#fastqueue"><code>fastqueue()</code></a>
* <a href="#push"><code>queue#<b>push()</b></code></a>
* <a href="#unshift"><code>queue#<b>unshift()</b></code></a>
* <a href="#pause"><code>queue#<b>pause()</b></code></a>
* <a href="#resume"><code>queue#<b>resume()</b></code></a>
* <a href="#idle"><code>queue#<b>idle()</b></code></a>
* <a href="#length"><code>queue#<b>length()</b></code></a>
* <a href="#getQueue"><code>queue#<b>getQueue()</b></code></a>
* <a href="#kill"><code>queue#<b>kill()</b></code></a>
* <a href="#killAndDrain"><code>queue#<b>killAndDrain()</b></code></a>
* <a href="#error"><code>queue#<b>error()</b></code></a>
* <a href="#concurrency"><code>queue#<b>concurrency</b></code></a>
* <a href="#drain"><code>queue#<b>drain</b></code></a>
* <a href="#empty"><code>queue#<b>empty</b></code></a>
* <a href="#saturated"><code>queue#<b>saturated</b></code></a>
* <a href="#promise"><code>fastqueue.promise()</code></a>

-------------------------------------------------------
<a name="fastqueue"></a>
### fastqueue([that], worker, concurrency)

Creates a new queue.

Arguments:

* `that`, optional context of the `worker` function.
* `worker`, worker function, it would be called with `that` as `this`,
  if that is specified.
* `concurrency`, number of concurrent tasks that could be executed in
  parallel.

-------------------------------------------------------
<a name="push"></a>
### queue.push(task, done)

Add a task at the end of the queue. `done(err, result)` will be called
when the task was processed.

-------------------------------------------------------
<a name="unshift"></a>
### queue.unshift(task, done)

Add a task at the beginning of the queue. `done(err, result)` will be called
when the task was processed.

-------------------------------------------------------
<a name="pause"></a>
### queue.pause()

Pause the processing of tasks. Currently worked tasks are not
stopped.

-------------------------------------------------------
<a name="resume"></a>
### queue.resume()

Resume the processing of tasks.

-------------------------------------------------------
<a name="idle"></a>
### queue.idle()

Returns `false` if there are tasks being processed or waiting to be processed.
`true` otherwise.

-------------------------------------------------------
<a name="length"></a>
### queue.length()

Returns the number of tasks waiting to be processed (in the queue).

-------------------------------------------------------
<a name="getQueue"></a>
### queue.getQueue()

Returns all the tasks be processed (in the queue). Returns empty array when there are no tasks

-------------------------------------------------------
<a name="kill"></a>
### queue.kill()

Removes all tasks waiting to be processed, and reset `drain` to an empty
function.

-------------------------------------------------------
<a name="killAndDrain"></a>
### queue.killAndDrain()

Same than `kill` but the `drain` function will be called before reset to empty.

-------------------------------------------------------
<a name="error"></a>
### queue.error(handler)

Set a global error handler. `handler(err, task)` will be called
when any of the tasks return an error.

-------------------------------------------------------
<a name="concurrency"></a>
### queue.concurrency

Property that returns the number of concurrent tasks that could be executed in
parallel. It can be altered at runtime.

-------------------------------------------------------
<a name="drain"></a>
### queue.drain

Function that will be called when the last
item from the queue has been processed by a worker.
It can be altered at runtime.

-------------------------------------------------------
<a name="empty"></a>
### queue.empty

Function that will be called when the last
item from the queue has been assigned to a worker.
It can be altered at runtime.

-------------------------------------------------------
<a name="saturated"></a>
### queue.saturated

Function that will be called when the queue hits the concurrency
limit.
It can be altered at runtime.

-------------------------------------------------------
<a name="promise"></a>
### fastqueue.promise([that], worker(arg), concurrency)

Creates a new queue with `Promise` apis. It also offers all the methods
and properties of the object returned by [`fastqueue`](#fastqueue) with the modified
[`push`](#pushPromise) and [`unshift`](#unshiftPromise) methods.

Arguments:
* `that`, optional context of the `worker` function.
* `worker`, worker function, it would be called with `that` as `this`,
  if that is specified. It MUST return a `Promise`.
* `concurrency`, number of concurrent tasks that could be executed in
  parallel.

<a name="pushPromise"></a>
#### queue.push(task) => Promise

Add a task at the end of the queue. The returned `Promise`  will be fulfilled
when the task is processed.

<a name="unshiftPromise"></a>
#### queue.unshift(task) => Promise

Add a task at the beginning of the queue. The returned `Promise`  will be fulfilled
when the task is processed.

## License

ISC

[ci-url]: https://github.com/mcollina/fastq/workflows/ci/badge.svg
[npm-badge]: https://badge.fury.io/js/fastq.svg
[npm-url]: https://badge.fury.io/js/fastq
[david-badge]: https://david-dm.org/mcollina/fastq.svg
[david-url]: https://david-dm.org/mcollina/fastq
