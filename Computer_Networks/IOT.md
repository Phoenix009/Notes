## Introduction:
"Things" getting connected to the Internet and getting "Smart"

What is needed for IoT?:
- "Smart" devices
- Communication
- Applications

*Why connect things to the internet?*
- IoT devices have power and CPU constraints
- Offloading of complicated tasks from IoT devices allows smaller and more energy efficient devices
- Host 'Smart' part of 'Dumb' devices
- Cloud applications can be updated at will of vendor
- No user interaction needed for updates

**Simplified Setup for IoT:**
Let all devices connect to the cloud with known endpoints controlled by the vendor
- Can be scaled and is under full control of the vendor
- Home network is unknown
- No direct access to devices, but they can connect to the outside
- No problem with dead or broken devices. No need to store state at vendor

## What is a Message Broker?
A pattern in software architecture
In out context:
- Reachable by all devices
- Receiving messages
- Keeping state
- Forwarding messages
Is just an intermediary that forwards messages between different components of the IoT

## MQTT - Message Queuing Telemetry Transport
- Core is the Message Broker
- Clients can subscribe to topics
- Clients can publish data for a topic/topics
- Clients interact only with the message broker
- Uses TCP usually a long-standing connection

### Subscribing to topics:
- Topics are organised in hierarchy. 
- Have naming similar to the file path.
- There are two wildcards:
	1. `+` - place holder matching single value
		Ex: `+/sw0` matches `/switches/sw0`
	2. `#` - place holder matching one or multiple values
		Ex: `#/sw0` matches `/switches/in/.../sw0`


## Security
Limited CPU resources is a major constraint while implementing security in IoT

- Confidentiality
	- Data should be transferred encrypted
	- Data processing in cloud -> cloud should be secure
- Integrity
- Availability
	- All cloud solutions rely on the internet
	- vendor cloud needs to be available too
- Safety (for IoT):
	- Depending on the devices serious injury on death are possible
	- Simple errors can get extremely expensive
	- Hardware based safety
- Privacy (for IoT):
	- Data from IoT can provide deep insights



