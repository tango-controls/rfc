---
domain: rfc.tango-controls.org
shortname: 5/PROPERTY
name: Property
status: raw
editor: Gwenaëlle Abeillé `<at synchrotron-soleil.fr - gwenaelle.abeille>`
contributors:
  - Vincent Hardion `<at maxiv.lu.se - vincent.hardion>`
  - Reynald Bourtembourg `<at esrf.fr - bourtemb>`
  - Andy Gotz <andy.gotz@esrf.fr>
---

This document describes the Property, a Tango concept representing one or more values to configure one of the following Tango elements namely Tango Device, Class, Attribute, System or a not associated to any Tango element, so called free properties. This document describes version 1.0 of the Property.

See also: 2/Device, 6/Database, 4/Attribute

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Property Specification

 A Property is a persisted item of Tango that is mainly used for configuration purposes. 4 types of properties exist:

 *  Class Property: a Property related to a Device Class (cf RFC-2).
 *  Device Property: a Property related to a Device (RFC-2).
 *  Attribute Property: a Property related to an Attribute (RFC-4).
 *  Free Property: a Property defined for the entire Control System (RFC-1) i.e not related to any Class, Device or Attribute.

### Goals

 The specification of Property aims to define a standard structure and type to use in any implementation of Tango.

Additionally, it aims to:

* Provide the initial information when one starts a Tango Device

* Be usable as keeping global variable in the Tango Control System

* Provide meta data complementing any data in Tango


### Use Cases

There are many use cases for the usage of a Property:

* To connect a real network equipment a Property can define the target IP address

* To change the representation of an information in order to be more user friendly a Property can define a new data format for the graphical user interface to convert the raw data.

* To configure some alarms on Attribute value.

* To memorize the Attribute value each time it changes.

## Specification

A Tango Property is a strict definition of a pair of key/value
* The Property SHALL have one key, called Property Name
* The Property SHALL have one value, which could be empty, called Property Value
* If the Device is started upon a Tango Database, Class, Device, Attribute and Free Properties MAY be persisted into the Tango Database (RFC6)
* If the Device is started without a Tango Database, the device and class properties MAY be persisted in a file (but no support for the attribute properties). 
* All Device and Class Properties MAY be directly modified and accessed through the database device or its file (TODO: to be moved to Request/Reply RFC)
* Attribute Properties SHOULD be modified and accessed at any time through the Device object (RFC-2) (TODO: to be moved to Request/Reply RFC)
* A Property Value MAY have a default value
* A Mandatory Property is a Property for which the Property Value SHALL NOT be null and SHALL not have a default value
* If the Device is started upon a Tango Database, the Class and Device Properties changes are historized in the Tango Database (RFC-6) (TODO: To be moved to Tango Database RFC)
* When a error occurs (i.e. network issue, Tango Database timeout...), a DevFailed exception MUST be thrown (RFC-x for DevFailed/Common types?) (TODO: To be moved to Request-Reply RFC)
* All Device and Class Properties MAY be loaded at init command or device start-up (RFC-8)
* A Device or Class Property MAY have a default value. 
* The Device Property Value loading flow in pseudo-code is (TODO to be moved to RFC-8):
	```
	property-value = getDevicePropertyFromTangoDBorFile(device-name, property-name)
	if(property-value is empty)
  		property-value = getClassPropertyFromTangoDBorFile(class-name, property-name)
	if(property-value is empty && default-value is not empty)
  		property-value = default-value
  	```
	* The Class property value loading flow is:
	```
	property-value = getClassPropertyFromTangoDBorFile(class-name, property-name)
	if(property-value is empty && default-value is not empty)
  		property-value = default-value
  	```
* The precedence of Property Value for a given Property Name SHALL follow this order of preference:
  1) the last Device Property Value
  2) the last Class Property Value
  3) the default Device Property Value
  4) the default Class Property Value
  
  Meaning that:
  * The last Property Value has precedence over the default Property Value
  * The Device Property Value has precedence over the Class Property
* All Device and Class Properties MAY be modified and accessed through the database device when using a Tango Database (RFC-6)
* A Tango Device MUST manage the following default Device Properties, which are called System Properties, see RFC-cache:

| Name | Default value |  Description |
| --- | --- | --- |
| poll_ring_depth | 10 |  	Polling buffer depth |
| cmd_poll_ring_depth | "" | List of command polling history depth |
| attr_poll_ring_depth | "" | List of attribute polling history depth |
| poll_old_factor | 4 | Data too old factor |
| min_poll_period | "" | Minimum polling period for attributes and commands |
| cmd_min_poll_period | "" | List of command minimum polling periods |
| attr_min_poll_period | "" | List of attribute minimum polling periods |
| polled_attr | "" | List of polled attribute's names |

* A Tango Device MUST manage the following default Device Properties, which are called Logging Properties, see RFC-logging:

| Name | Default value |  Description |
| --- | --- | --- |
| logging_level | WARN | initial Device logging level (FATAL, ERROR, WARN, INFO, DEBUG, OFF) |
| logging_path | "/tmp/tango-<logging name>" or "C:/tango-<logging name> (Windows)" | Logging file path |
| logging_rft | 20480 (20 Mega bytes) | Logging rolling file threshold in bytes |
| logging_target | "" | Initial Device logging target, a file or a device. ex: "file:/tmp/file.log", "device:log/device/logger.1" |

* An attribute MUST manage the following default attribute properties (TODO: move this to specifics RFCs: Attribute Configuration properties / Attribute Event properties / Attribute Properties?):
	* Attribute Configuration properties :
	
| Name | Default value |  Description |
| --- | --- | --- |
| label | "" | |
| description | "No description" | A description of the attribute |
| unit | "No unit" | |
| standard_unit | "No standard unit" | |
| display_unit | "No display unit" | Unit to display |
| format | if attribute type is a string, "%s". If attribute type is float or double, "%6.2f". "Not specified" otherwise | Display format |

	* Attribute Configuration properties, see RFC-4:
	
| Name | Default value |  Description |
| --- | --- | --- |
| min_value | "Not specified" | Only for numeric attributes. Minimum allowed attribute value.|
| max_value | "Not specified" | Only for numeric attributes. Maximum allowed attribute value. |
| min_alarm | "Not specified" | Only for numeric attributes. Attribute's value minimum alarm |
| max_alarm | "Not specified" | Only for numeric attributes. Attribute's value maximum alarm |
| min_warning | "Not specified" | Only for numeric attributes. Attribute's value minimum warning|
| max_warning | "Not specified" | Only for numeric attributes. Attribute's value maximum warning|
| delta_t | "Not specified" | Only for numeric attributes. Attribute's value delta time used to process delta Alarm. |
| delta_val | "Not specified" | Only for numeric attributes. Attribute's value delta value used to process delta Alarm. |
| enum_labels | "Not specified" |  Only used for DevEnum attributes. List of enumerated labels. |
| __root_att | "Not specified" | Only used for forwarded attributes. Name of the underlying attribute. |
| __value | "" | Only used for memorized attributes. Memorized attribute's value. |

	* Attribute Event properties, see RFC-12 and RFC-13 :
| Name | Default value |  Description |
| --- | --- | --- |
| event_period | "Not specified" | Change event period. |
| abs_change | "Not specified" | Value to trigger an absolute change event.  |
| rel_change | "Not specified" | Value to trigger a relative change event.  |
| archive_period | "Not specified" | Archiving event period.|
| archive_abs_change | "Not specified" | Value to trigger an absolute archiving event. |
| archive_rel_change | "Not specified" | Value to trigger an relative archiving event.  |

### Naming convention
* The Property's Name SHALL use the following convention:
``` ABNF
alphanum = ALPHA / DIGIT 
underscore = %x5F
device_property_name = 1*1ALPHA 0*254(alphanum / underscore)
class_property_name = 1*1ALPHA 0*254(alphanum / underscore)
free_property_name = 1*1ALPHA 0*254(alphanum / underscore)
attribute_property_name = 1*1(ALPHA / underscore) 0*254(alphanum / underscore)
```
* The Property Value MUST use the following convention:
``` ABNF
 property-value = CHAR / 1*CHAR CR
 ```

TODO: Fix property value which depends on property Tango type, it can be empty and can contain several lines in case of
properties with array types.

### Properties for Device without Database

* A Device started without a Tango Database MAY use a file to persist its Properties. Here is an example file content to show the syntax that must be used in this file:	
```
# --- my/tango/device properties
my/tango/device->myProp:value

# This is a comment

CLASS/MyClass->myClassProp: 10
```

TODO: Specify the supported Tango types for Properties (DevBoolean, DevShort, DevUShort, ..., array of DevShort,...)
TODO: Add some precisions about special words like "NaN" and "Inf" and their meaning when using some DevDouble or DevFloat Properties