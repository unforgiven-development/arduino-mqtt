# **arduino-mqtt** _("PahoMQTT")_ Arduino Library #

[![Build Status](https://travis-ci.org/unforgiven-development/arduino-mqtt.svg?branch=master)](https://travis-ci.org/unforgiven-development/arduino-mqtt)


**MQTT library for Arduino**, _based on the **Eclipse Paho** projects_

This library bundles the [Embedded MQTT C/C++ Client][1] library of the Eclipse Paho project and adds a thin wrapper to get an _Arduino-like_ API. Additionally, there is an drop-in
alternative for the **Arduino Yùn** that uses a _python-based_ client on the Linux processor and a binary interface to lower program space usage on the Arduino side.

The first release of the library only supports **QoS0** and the basic features to get going. In the next releases more of the features will be available. Please create an issue if
you need a specific functionality.


## INSTALLATION ##

Download [version **1.10.2**][2] of the library from GitHub as a ZIP file, and install the library manually via the **Sketch** --> **Include Library** --> **Add .ZIP Library...**
menu entry in the **Arduino IDE**.

**Please note that my modification of this library is currently not available in the _Arduino Library Manager_.**


## MODIFICATIONS ##

### LIBRARY NAMING CONFLICT RESOLUTION ###

_Note that the primary modification made is to resolve the library naming conflict._

All of the files in the library following the naming format **MQTT_(...)_** been renamed to **PahoMQTT_(...)_** to resolve naming conflicts with other Arduino libraries.


### OTHER MODIFICATIONS ###

Additionally, some experimentation with **QoS** settings and _buffer sizes_ has been occurring in the **pahomqtt-tweaked** branch.


## COMPATIBILITY ##

This library is known to work with the following hardware:
- **Arduino** _(AVR-based boards)_
	- **Arduino** Ethernet Shield
	- **Arduino** WiFi Shield
	- **Arduino** WiFi101 Shield
	- **Arduino** Yùn Shield _(as a process on the **Linux core**)_
- **Arduino** Yùn _(as a process on the **Linux core**)_
- **ESP8266** modules & boards



## EXAMPLES ##

The following examples show how you can use the library with various Arduino compatible hardware:

- [Arduino Yùn & Yùn-Shield _(PahoMQTTClient)_][5a]
- [Arduino Yùn & Yùn-Shield _(YunPahoMQTTClient)_][5b]
	- [With SSL support...][5c]
- [Arduino Ethernet Shield][5d]
- [Arduino WiFi Shield][5e]
- [Adafruit HUZZAH ESP8266][5f]
	 - [With SSL support...][5g]
- [Arduino/Genuino WiFi101 Shield][5h]
	- [With SSL support...][5i]

Other shields and boards should work if they also provide a [Client][6]-based network implementation.

## CAVEATS ##

- The maximum size for packets being published and received is set by default to 384 bytes. To change that value, you need to download the library manually and change the value in
  the following file: [src/PahoMQTTClient.h][7].
- On the **ESP8266**, it has been reported that an additional `delay(10);` after `client.loop();` fixes many stability issues with WiFi connections.


## EXAMPLE IMPLEMENTATION ##

The following example uses an Arduino Yùn and the MQTTClient to connect to [shiftr.io][8]. You can check on your device after a successful connection here: <https://shiftr.io/try>.

```c++
#include <Bridge.h>
#include <YunClient.h>
#include <MQTTClient.h>

YunClient net;
MQTTClient client;

unsigned long lastMillis = 0;

void setup() {
	Bridge.begin();
	Serial.begin(115200);
	client.begin("broker.shiftr.io", net);

	connect();
}

void connect() {
	Serial.print("connecting...");
	while (!client.connect("arduino", "try", "try")) {
		Serial.print(".");
		delay(500);
	}

	Serial.println("\nconnected!");

	client.subscribe("/example");
	//client.unsubscribe("/example");
}

void loop() {
	client.loop();

	if (!client.connected()) {
		connect();
	}

	// publish a message roughly every second.
	if (millis() - lastMillis > 1000) {
		lastMillis = millis();
		client.publish("/hello", "world");
	}
}

void messageReceived(String topic, String payload, char * bytes, unsigned int length) {
	Serial.print("incoming: ");
	Serial.print(topic);
	Serial.print(" - ");
	Serial.print(payload);
	Serial.println();
}
```

## API ##

Initialize the object using the **hostname** of the broker, the brokers **port** (default: `1883`) and the underlying _**Client** class_ for network transport:

```c++
boolean begin(const char * hostname, Client& client);
boolean begin(const char * hostname, int port, Client& client);
```

- Specify port `8883` when using SSL clients for secure connections.
- The `YunMQTTClient` does not need the `client` parameter.

Set the will message that gets registered on a connect:

```c++
void setWill(const char * topic);
void setWill(const char * topic, const char * payload);
```

Connect to broker using the supplied client id and an optional username and password:

```c++
boolean connect(const char * clientId);
boolean connect(const char * clientId, const char * username, const char * password);
```

- This functions returns a value that indicates if the connection has been established successfully.

Publishes a message to the broker with an optional payload:

```c++
boolean publish(String topic);
boolean publish(String topic, String payload);
boolean publish(const char * topic, String payload);
boolean publish(const char * topic, const char * payload);
boolean publish(const char * topic, char * payload, unsigned int length);
boolean publish(MQTTMessage * message)
```

- The last function can be used to publish messages with more low level attributes like `retained`.

Subscribe to a topic:

```c++
boolean subscribe(String topic);
boolean subscribe(const char * topic);
```

Unsubscribe from a topic:

```c++
boolean unsubscribe(String topic);
boolean unsubscribe(const char * topic);
```

Sends and receives packets:

```c++
void loop();
```

- This function should be called in every `loop`.

Check if the client is currently connected:

```c++
boolean connected();
```

Disconnects from the broker:

```c++
boolean disconnect();
```


[1]:	<https://eclipse.org/paho/clients/c/embedded/>
[2]:	<https://github.com/unforgiven-development/arduino-mqtt/releases/download/v1.10.2/mqtt.zip>

[5a]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoYun_MQTTClient/ArduinoYun_MQTTClient.ino>
[5b]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoYun_YunMQTTClient/ArduinoYun_YunMQTTClient.ino>
[5c]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoYun_YunMQTTClient_SSL/ArduinoYun_YunMQTTClient_SSL.ino>
[5d]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoEthernetShield/ArduinoEthernetShield.ino>
[5e]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoWiFiShield/ArduinoWiFiShield.ino>
[5f]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/AdafruitHuzzahESP8266/AdafruitHuzzahESP8266.ino>
[5g]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/AdafruitHuzzahESP8266_SSL/AdafruitHuzzahESP8266_SSL.ino>
[5h]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoWiFi101/ArduinoWiFi101.ino>
[5i]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/examples/ArduinoWiFi101_SSL/ArduinoWiFi101_SSL.ino>
[6]:	<https://www.arduino.cc/en/Reference/ClientConstructor>
[7]:	<https://github.com/unforgiven-development/arduino-mqtt/blob/master/src/PahoMQTTClient.h#L5>
[8]:	<htt[s://shiftr.io>]