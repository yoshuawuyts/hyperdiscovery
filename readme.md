# hyperdiscovery

[![Travis](https://api.travis-ci.org/karissa/hyperdiscovery)](https://travis-ci.org/karissa/hyperdiscovery)

Join the p2p swarm for hypercore and hyperdrive feeds.

```
npm install hyperdiscovery
```

## Usage

Run the following code in two different places and they will replicate the contents of the given `ARCHIVE_KEY`.

```js
var hyperdrive = require('hyperdrive')
var memdb = require('memdb')
var swarm = require('hyperdiscovery')

var drive = hyperdrive(memdb())
var archive = drive.createArchive('ARCHIVE_KEY')

var sw = swarm(archive)
sw.on('connection', function (peer, type) {
  console.log('got', peer, type) // type is 'webrtc-swarm' or 'discovery-swarm'
  console.log('connected to', sw.connections.length, 'peers')
  peer.on('close', function () {
    console.log('peer disconnected')
  })
})
```

Will use `discovery-swarm` to attempt to connect peers. Uses `datland-swarm-defaults` for peer introduction defaults on the server side, which can be overwritten (see below).

The module can also create and join a swarm for a hypercore feed:

```js
var hypercore = require('hypercore')
var memdb = require('memdb')
var swarm = require('hyperdiscovery')

var core = hypercore(memdb())
var feed = core.createFeed()
var sw = swarm(feed)
```

## API

### `var sw = swarm(archive, opts)`

Join the p2p swarm for the given feed. The return object, `sw`, is an event emitter that will emit a `peer` event with the peer information when a peer is found.

### sw.connections

Get the list of currently active connections.

##### Options

  * `upload`: bool, upload data to the other peer?
  * `download`: bool, download data from the other peer?
  * `port`: port for discovery swarm
  * `utp`: use utp in discovery swarm
  * `tcp`: use tcp in discovery swarm

Defaults from datland-swarm-defaults can also be overwritten:

  * `dns.server`: DNS server
  * `dns.domain`: DNS domain
  * `dht.bootstrap`: distributed hash table bootstrapping nodes
