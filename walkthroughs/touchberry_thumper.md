## Touchberry Thumper

This walkthrough will guide you through the TouchBerry Thumper setup.

As a teacher you should make sure to boot the Thumper first things first.
Next use a local machine (laptop for example) to connect both to the local network
and to the WiFi access point of the Thumper. Launch the NodeJs Touchberry Thumper Proxy
script to pipe the REST commands from the student's Touchberry Pi Shields to the Thumper.

As a student you can follow the chapters indicated here step by step to setup your project.
This should enable you to control the Thumper and NeoPixels using your Touchberry Pi shield.

### Hardware Requirements

* 6 wheel Thumper with Raspberry Pi and Wireless Access Point
* Computer that is connected to both LAN and Wireless AP of Thumper
* TouchBerry Pi Shield and Raspberry Pi 2 for each student or group of students

### Software Requirements

* Thumper motor controller should be equipped with latest firmware [https://github.com/BioBoost/thumper_trex_firmware](https://github.com/BioBoost/thumper_trex_firmware).
* Raspberry Pi on Thumper should be running Node Thumper Control [https://github.com/BioBoost/node_thumper_control](https://github.com/BioBoost/node_thumper_control)
* Computer that is connected to LAN and AP should be running the Touchberry Thumper Proxy [https://github.com/BioBoost/touchberry_thumper_proxy](https://github.com/BioBoost/touchberry_thumper_proxy)

* The Raspberry Pi 2 of the students should be running
  * the latest Raspbian distribution;
  * the C++ TouchBerry i2c master application that you wrote
  * the Ruby script that sends the REST requests based on the commands received from the C++ application

### Teacher Setup

#### Touchberry Thumper Proxy

Since the Raspberry Pi 2 with the Touchberry Shield does not have a Wifi dongle we need a way to *proxy* the commands from the devices to the Thumper.
This can easily be achieved by running a *proxy* script on a local machine that is both connected to the local LAN as to the wireless Thumper access point.

A very simple script can be created in JavaScript and run using NodeJs.

```javascript
var request = require('request');
var http = require('http');

var argv = require('minimist')(process.argv.slice(2));

var thumper = "http://192.168.1.100:3000"
if ("thumper" in argv) {
  thumper = "http://" + argv["thumper"] + ":3000"
}

console.log("Sending to REST interface @" + thumper)

http.createServer(function (req, resp) {
  req.pipe(request(thumper + req.url)).pipe(resp)
  console.log("Piping request: " + req.method + " | to " + req.url)

  req.on('data', function(chunk) {
    console.log(chunk.toString());
  });

}).listen(3000);
```

This script listens on port 3000 for incoming HTTP requests and pipes them through to the Thumper. The replies are send back to the original requester.
The ip address of the Raspberry Pi on the Thumper can be passed to the script as a command line argument as shown in the following launch command:

```shell
node proxy.js --thumper=192.168.1.100
```

The port defaults both incoming as outgoing to `3000`.

The full instructions and repository can be found at [https://github.com/BioBoost/touchberry_thumper_proxy](https://github.com/BioBoost/touchberry_thumper_proxy).

Once the Thumper and script are up and running you can test the setup by surfing to the device running the script on port 3000 (ex. http://10.0.0.33:3000). You should get the following output

```json
{"message":"Hello and welcome to the Thumper Control RESTful API"}
```

The proxy script should also indicate a successful forward.

#### Student Setup

The following sections will provide the necessary information for you to create the
C++ TouchBerry i2c master application and Ruby script that sends the REST requests based on the commands received from the C++ application.
