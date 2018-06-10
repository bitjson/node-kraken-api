# node-kraken-api

[![NPM](https://nodei.co/npm/node-kraken-api.png)](https://nodei.co/npm/node-kraken-api/)

Interfaces with the Kraken cryptocurrency exchange API. Observes rate limits. Parses response JSON, converts stringed numbers, and normalizes timestamps. Facilitates persistent data syncing.

## Getting Started

### Prerequisites

Node.JS version 8.7.0 or above.

### Installing

```console
npm i node-kraken-api
```

### Testing

The following command will test the package for errors. Testing provided by jest library. Testing may take several minutes to complete.

___Note:___ In order for authenticated testing to occur, a valid auth.json file must be available in the root directory of the package. Please see the [configuration](#configuration) instructions below. An empty auth.json file has been provided; please fill the 'key', 'secret', and 'tier' properties accordingly.

__Modifying the provided auth.json for authenticated testing (use a read-only key to be safe!):__

_NOTE: replace 'nano' with your preferred editor._
```console
nano /path/to/node_modules/node-kraken-api/auth.json
```

__Installing the jest library:__
```console
npm i --prefix /path/to/node_modules/node-kraken-api jest
```

__Running the testing scripts:__

```console
npm test --prefix /path/to/node_modules/node-kraken-api
```

### Deployment

__Loading the module:__
```js
const kraken = require('node-kraken-api')
```

__Public client instantiation:__
```js
const api = kraken()
```

__Private client instantiation (with authenticated [configuration](#configuration)):__
```js
const api = kraken(require('./auth.json'))
```

Or:

```js
const api = kraken({ key: '****', secret: '****', tier: '****' })
```

__Instantiation with any number of configuration settings (see [configuration](#configuration)):__
```js
const api = kraken(require('./config.json'))
```

Or:

```js
const api = kraken(require('./config.js'))
```

Or:

```js
const api = kraken({ key: '****', secret: '****', tier: '****', parse: { dates: false } })
```

### Usage

__Making a single call:__

_Using promises:_
```js
api.call('Time').then(
  data => console.log(data)
).catch(
  err => console.error(err)
)
```

_Using callbacks:_
```js
api.call('Time', (err, data) => {
  if (err) {
    console.error(err)
  } else {
    console.log(data)
  }
})
```

_Using promises (with Kraken method options):_
```js
api.call('Depth', { pair: 'XXBTZUSD', count: 1 }).then(
  data => console.log(data)
).catch(
  err => console.error(err)
)
```

_Using callbacks (with Kraken method options):_
```js
api.call('Depth', { pair: 'XXBTZUSD', count: 1 }, (err, data) => {
  if (err) {
    console.error(err)
  } else {
    console.log(data)
  }
})
```

__Working with data syncing:__

_Creating a sync object:_
```js
const timeSync = api.sync('Time')
// logs {}
console.log(timeSync.data)

// logs { unixtime: 1528607559000, rfc1123: 1528607559000 }
// (unless call had failed)
setTimeout(() => console.log(timeSync.data), 5000)
```

_Using syncing promises:_
```js
const api = require('./')()
const timeSync = api.sync('Time')

let i = 0
const continuouslyLogUpdates = async () => {
  while(i++ < 20) {
    try {
      console.log(await timeSync.next())
    } catch(e) {
      console.error(e)
    }
  }
}

continuouslyLogUpdates()
```

_Creating a sync object (using a listener callback):_
```js
const timeSync = api.sync('Time', (err, data) => {
  if (err) console.error(err)
  else if (data) console.log(data)
}
```

_Adding a listener callback after creation:_
```js
const timeSync = api.sync('Time')
timeSync.addListener((err, data) => {
  if (err) console.error(err)
  else if (data) console.log(data)
})
```

_Closing a sync operation:_
```js
const timeSync = api.sync('Time')
timeSync.addListener((err, data) => {
  // stops executing (and syncing) after ~5000 ms
  if (err) console.error(err)
  else if (data) console.log(data)
})
setTimeout(timeSync.close, 5000)
```

_Re-opening a sync operation:_
```js
const timeSync = api.sync('Time')
timeSync.addListener((err, data) => {
  // stops executing (and syncing) after ~5000 ms
  // starts executing (and syncing) again after ~10000 ms
  if (err) console.error(err)
  else if (data) console.log(data)
})
setTimeout(timeSync.close, 5000)
setTimeout(timeSync.open, 10000)
```

_Removing a sync listener callback:_
```js
const timeSync = api.sync('Time')
const listener = (err, data) => {
  // stops executing after ~5000 ms
  // (timeSync will continue to stay up to date)
  if (err) console.error(err)
  else if (data) console.log(data)
}
timeSync.addListener(listener)
setTimeout(() => timeSync.removeListener(listener), 5000)
```

_Viewing historical errors:_
```js
const timeSync = api.sync('BadMethodExample')
setTimeout(() => {
  console.log(timeSync.errors)
  // logs the time of the error
  console.log(timeSync.errors[0].time)
}, 10000)
```

<a name='configuration'></a>
## Configuration

During creation of the API instance, a configuration object may be provided for authenticated calls and other options. This may be provided by using <code>require('./config.js')</code> or <code>require('./config.json')</code> if this file has been prepared already, or by simply providing an object programmatically.

Configuration specifications are detailed in the documentation [here](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/Settings.md#~Config)

## Documentation

### See:
  + [node-kraken-api](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/node-kraken-api.md)
  + [API](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/API.md)
    + [Calls](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/API/Calls.md)
      + [genRequestData](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/API/Calls/genRequestData.md)
      + [loadCall](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/API/Calls/loadCall.md)
      + [signRequest](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/API/Calls/signRequest.md)
    + [RateLimits](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/API/RateLimits.md)
      + [limiter](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/API/RateLimits/limiter.md)
    + [Syncing](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/API/Syncing.md)
      + [loadSync](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/API/Syncing/loadSync.md)
  + [Settings](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/Settings.md)
    + [defaults](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Settings/defaults.md)
  + [Tools](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/Tools.md)
    + [ms](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Tools/ms.md)
    + [parseNested](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Tools/parseNested.md)
    + [readFileJSON](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Tools/readFileJSON.md)
    + [tryDirectory](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Tools/tryDirectory.md)
    + [writeFileJSON](https://github.com/jpcx/node-kraken-api/blob/develop/docs/modules/Tools/writeFileJSON.md)
  + [Kraken](https://github.com/jpcx/node-kraken-api/blob/develop/docs/namespaces/Kraken.md)


## Versioning

Versioned using [SemVer](http://semver.org/). For available versions, see the [Changelog](https://github.com/jpcx/node-kraken-api/blob/develop/CHANGELOG.md).

## Contribution

Please raise an issue if you find any. Pull requests are welcome!

## Author

  + **Justin Collier** - [jpcx](https://github.com/jpcx)

## License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/jpcx/node-kraken-api/blob/develop/LICENSE) file for details