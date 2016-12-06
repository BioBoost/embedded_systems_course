## Touchberry Thumper

This walkthrough will guide you through the TouchBerry Thumper setup.

### General idea

The Thumper can be controlled using it's RESTfull web interface that is hosted
on port 3000. The API can be found at [https://github.com/BioBoost/node_thumper_control](https://github.com/BioBoost/node_thumper_control).

The student have a Raspberry Pi 2 and a TouchBerry shield at their disposal (with Qt1070 touch sensor and some touch pads).
The status of the TouchBerry shield should be read from the Qt1070 chip using I2c by the use of a C++ master application. This application
should write its findings to a file in the directory `/tmp/touch` under the form of instructions such as 'forward', 'left', ...

The instructions are than detected by a Ruby script which sends them to the Thumper RESTfull API.

However since the Raspberry Pi's of the students don't have WiFi dongles a local device will need to act as a proxy forwarding commands
and replies between the Pi's and the Thumper.

### Setup

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

##### The Ruby REST script

A device in linux is most often accessed through a file such as '/dev/i2c-1' or '/dev/ttyACM0'. We would like to follow the
same road with the TouchBerry application. Meaning we want the state of the TouchBerry to be accessed through a simple file.

Let's assume that we can read the state of the shield by reading from a file inside the `/tmp/touch` directory. This file could contain the following states:

* left
* right
* forward
* reverse
* stop
* a
* b
* x

Where stop is the state when no key is pressed on the board.

An example script to start from is given below. This script is able to detect the changes of the files inside a `/tmp/touch` directory (create for example a file called `thumper` inside this directory) and send a REST request to a given host (this should be the laptop of the teacher).

```ruby
#!/usr/bin/env ruby

require 'listen'
require 'net/http'
require 'json'

class ThumperRestInterface

	@@DRIVE_SPEED = 70
	@@ID = 'mark'

	def initialize host='http://localhost:3000'
		@host = host
	end

	def strobe
		uri = URI(@host + '/neopixels/effects/strobe/0')
		req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
		req.body = {red: 0, green: 240, blue: 0, delay: 50, id: @@ID }.to_json
		send_request uri, req
	end

	def dim
		uri = URI(@host + '/neopixels/strings/0')
		req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
		req.body = {red: 0, green: 0, blue: 0, id: @@ID }.to_json
		send_request uri, req
	end

	def left
		drive @@DRIVE_SPEED, -@@DRIVE_SPEED
	end

	def right
		drive -@@DRIVE_SPEED, @@DRIVE_SPEED
	end

	def forward
		drive @@DRIVE_SPEED, @@DRIVE_SPEED
	end

	def reverse
		drive -@@DRIVE_SPEED, -@@DRIVE_SPEED
	end

	def stop
		drive 0, 0
	end

	def drive leftspeed, rightspeed
		uri = URI(@host + '/speed')
		req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
		req.body = {left_speed: leftspeed, right_speed: rightspeed, id: @@ID }.to_json
		send_request uri, req
	end

	def send_request uri, req
		res = Net::HTTP.start(uri.hostname, uri.port) do |http|
			http.request(req)
		end
	end
end

thumper = ThumperRestInterface.new

listener = Listen.to('/tmp/touch') do |modified|
  puts "modified absolute path: #{modified}"
	File.readlines(modified.first).each do |instruction|
		instruction.strip!

		if thumper.respond_to?(instruction.to_sym)
			thumper.send instruction
		else
			thumper.stop
		end

	end
end
listener.start # not blocking

sleep
```

The full script with README can be found at [https://github.com/BioBoost/touchberry_thumper_ruby_commander](https://github.com/BioBoost/touchberry_thumper_ruby_commander)

While the script needs to run on your Raspberry Pi, you can however clone it to your development machine first to change it and later secure copy it to the Pi.

While the script is pretty straight forward, the following block of code does require some
extra attention:

```ruby
if thumper.respond_to?(instruction.to_sym)
  thumper.send instruction
else
  thumper.stop
end
```

The code above will take the line read from the file after being stripped (whitespace is removed)
and check if the `ThumperRestInterface` class has a method with the name of the instruction from the file.
If it does, it will call it. Otherwise it will stop the Thumper as a precaution.

So this means that if you want to implement new instructions you simply need to add a method with that
name to the `ThumperRestInterface` class and make your C++ application output that instruction to the file.

Also make sure to change the ID from 'mark' to something personal. That will sure that each request is
personalized and easily identifiable in the proxy logs.

To test this script you should first create the directory `/tmp/touch`. Touch a file inside it, for example `thumper`. Next run the Ruby script using `./thumper_touch.rb` (after doing a `bundle install`). Now if you execute the following command the lights of the Thumper should strobe:

```shell
echo "strobe" > /tmp/touch/thumper
```

or dim using

```shell
echo "dim" > /tmp/touch/thumper
```

##### The TouchBerry I2C master application

Solution for the teacher to this can be found @ [https://github.com/BioBoost/embedded_systems_course_solutions/tree/master/i2c_qt1070_master_pi](https://github.com/BioBoost/embedded_systems_course_solutions/tree/master/i2c_qt1070_master_pi)

This application should already be working from the previous lab sessions.

However now you will need to add the functionality to write the given state of the TouchBerry
to a file inside the `/tmp/touch` directory. If that works, you should be able to command the Thumper.

### Final notes

Good Luck !
