# SeleneParser.js

Parsers and converters for Selene packets

Dependencies: `Buffer` 

#### Properties

`Object` **TYPES** -- Holds descriptions of Selene PacketTypes. Keys are type names and type codes

---

## PacketType

Inherits: None

Holds information on a Selene packet type

#### Options

`Function` **fromBuffer** -- Sets .fromBuffer()

`Boolean` **hasPin** -- Sets .hasPin

`Number` **size** -- Sets .size

`Function` **toBuffer** -- Sets .toBuffer()

`Function` **validate** -- Sets .validate()

#### Properties

`Boolean` **hasPin** -- Does this type of packet have a pin #

`Number` **size** -- Size of payload in bytes. -1 if payload has variable size

`Number` **typeCode** -- Code # for type, used in binary representations

`String` **typeName** -- Human-readable type name, used in MQTT topics

#### Methods

`?` **fromBuffer**`(Buffer buffer)` -- Converts a valid payload buffer into a value

`Buffer` **toBuffer**`(* v)` -- Converts a value into a valid payload buffer

`Boolean` **validate**`(Buffer payload)` -- Tests if a payload is valid. Payloads are assumed to be buffers of the correct size

---

## Packet

Inherits: None

Represents a Selene packet

```
var Packet = require('./SelenePacket.js').Packet;

// Read a packet from an MQTT message (saying pin 2 at device 0x3D is set to 5)
var packet = Packet.fromMqtt('Se/3D/pin/2', new Buffer([5, 0, 0, 0]));
packet.address; // 0x3D
packet.type; // 'pin'
packet.pin; // 2
packet.value; // 5
packet.isRequest; // false

// Generate a packet and convert to an MQTT message (requesting device 0x3D to set pin 2 to 7)
var mqtt = new Packet(0x3D, 'pin', 2, 7, true).toMqtt();
mqtt.topic; 'Se/3D/pin/2/r'
mqtt.message; \<Buffer 07 00 00 00\>

// Read a packet from a binary buffer (saying pin 2 at device 0xA0 is set to 1023)
var packet = Packet.fromBuffer(new Buffer([0x53, 0xA0, 0, 0, 0, 5, 2, 0, 0, 0, 0, 0xFF, 3, 0, 0]));
packet.address; // 0xA0
packet.type; // 'pin'
packet.pin; // 2
packet.value; // 1023
packet.isRequest; // false

// Generate a packet and convert to a Buffer (requesting device 0xA0 to set pin 1 to 32)
var buffer = new Packet(0xA0, 'pin', 1, 32, true).toBuffer();
buffer; // \<Buffer 53 A0 00 00 00 05 01 80 00 00 00 20 00 00 00\>

// Can chain buffer and MQTT conversions
var mqtt = Packet.fromBuffer(some_buffer).toMqtt();
var buffer = Packet.fromMqtt(some_topic, some_message).toBuffer();
```

#### Properties

`Number` **address** -- Selene address. Must be a 32-bit unsigned integer

`Boolean` **isRequest** -- Request flag

`Buffer` **payload** -- Binary payload sent in MQTT packets. If set, will update .value

`Number` **pin** -- Virtual pin # for a μC. Must be an 8-bit unsinged integer

`String` **type** -- Human-readable type name. If set, will update .typeCode and .typeSpec

`Number` **typeCode** -- Type code #. If set, will update .type and .typeSpec

`PacketType` **typeSpec** -- Specifies packet type and related details. If set, will update .type and .typeCode

`?` **value** -- Value extracted from .payload. If set, will update .payload

#### Methods

`Packet|null` **Packet.fromBuffer**`(Buffer buffer)` -- Extracts Packets from valid buffers

`Packet|null` **Packet.fromMqtt**`(String topic, Buffer message)` -- Extracts Packets from valid MQTT messages

`Buffer` proto **toBuffer**`()` -- Does exactly what it says

`Object` proto **toMqtt**`()` -- Converts Packet to { String topic, Buffer message } object

`Boolean` proto **validate**`()` -- Tests if a Packet is valid

