# ubnt-unifi 

[![NPM version](https://badge.fury.io/js/ubnt-unifi.svg)](http://badge.fury.io/js/ubnt-unifi)
[![Dependency Status](https://img.shields.io/gemnasium/hobbyquaker/ubnt-unifi.svg?maxAge=2592000)](https://gemnasium.com/github.com/hobbyquaker/ubnt-unifi)
[![Build Status](https://travis-ci.org/hobbyquaker/ubnt-unifi.svg?branch=master)](https://travis-ci.org/hobbyquaker/ubnt-unifi)
[![XO code style](https://img.shields.io/badge/code_style-XO-5ed9c7.svg)](https://github.com/sindresorhus/xo)
[![License][mit-badge]][mit-url]

> ubnt-unifi is a Node.js module that allows you to listen for events from and call methods on the UniFi API (UniFi is 
Ubiquiti Networks wifi controller software).

This is a fork of [unifi-events](https://github.com/oznu/unifi-events), heavily modified to suite my needs.


## Requirements

* Node.js v6 or later
* [UniFi-Controller](https://www.ubnt.com/download/unifi) v5

## Installation

`$ npm install ubnt-unifi`


## Example

```javascript
const Unifi = require('ubnt-unifi')

let unifi = new Unifi({
  host: 'https://demo.ubnt.com',        // The hostname or ip address of the unifi controller (default: 'unifi')
  username: 'admin',                    // Username (default: 'admin').
  password: 'ubnt',                     // Password (default: 'ubnt').
  site: 'default',                      // The UniFi site to connect to (default: 'default').
  insecure: true                        // Allow connections if SSL certificate check fails (default: false).
});

// Listen for any event
unifi.on('**', function (data) {
  console.log(this.event, data);
});
```

## Events

ubnt-unifi uses [EventEmitter2](https://github.com/asyncly/EventEmitter2) and namespaced events. 

### namespace `ctrl`

These events indicate the status of the connection to the UniFi controller

* `ctrl.connect` - emitted when the connection to the controller is established
* `ctrl.disconnect` - emitted when the connection to the controller is lost
* `ctrl.error` - 
* `ctrl.reconnect` - 


### namespaces `wu`, `wg`, `lu`, ...

This JSON file shows all possible events: https://demo.ubnt.com/manage/locales/en/eventStrings.json?v=5.4.11.2
The prefix `EVT_` gets stripped, the first underscore is replaced by the namespace separating dot, everything is 
converted to lower case. Some events such as `EVT_AD_LOGIN` (Admin Login) are not emitted by the UniFi Controller.


#### Example Wireless User events

* `wu.connected` - Wireless User connected
* `wu.disconnected` - Wireless User disconnected
* `wu.roam` - Wireless User roamed from one AP to another
* `wu.roam_radio` - Wireless User changed channel on the same AP

#### Example Wireless Guest Events

* `wg.connected` - Wireless Guest connected
* `wg.disconnected` - Wireless Guest disconnected
* `wg.roam` - Wireless Guest roamed from one AP to another
* `wg.roam_radio` - Wireless Guest changed channel on the same AP
* `wg.authorization_ended` - Wireless Guest became unauthorised

#### Wildcard usage

Example listing for events on Guest Wireless networks only:

```javascript
unifi.on('wg.*', function (data) {
  console.log(this.event, data);
})
```

Example listening for connected events on all network types:

```javascript
unifi.on('*.connected', function (data) {
  console.log(this.event, data);
});
```

## Methods

Following methods operate on the configured site. The path gets prefixed with 
`https://<host>:<port>/api/s/<site>/`. To get an available API endpoints you can use the 
[UniFi-API-browser](https://github.com/Art-of-WiFi/UniFi-API-browser).

These methods are returning a promise.


#### get(path)

Get a list of all clients:

```javascript
unifi.get('stat/sta').then(console.log);
```

Get infos of a specific client:

```javascript
unifi.get('stat/user/<mac>').then(console.log);
```

#### del(path)


#### post(path, body)

Enable all LEDs of all APs:

```javascript
unifi.post('set/setting/mgmt', {led_enabled: true}).then(console.log);
```


## License

MIT © 2018 [Sebastian Raff](https://github.com/hobbyquaker)    
MIT © 2017-2018 [oznu](https://github.com/oznu)

[mit-badge]: https://img.shields.io/badge/License-MIT-blue.svg?style=flat
[mit-url]: LICENSE
