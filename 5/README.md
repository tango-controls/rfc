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

See also: 2/Device, X/Database, X/Attribute, X/Class

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Property Specification

 A Property is a persisted item of Tango that is mainly used for configuration purposes. 4 types of properties exist:

 *  Class property: a property that is attached to a Device Class (cf RFC-9). This property is accessible from all devices of the class
 *  Device property: a property that is attached to a Device (RFC-2). This property is accessible from only one device
 *  Attribute property: a property that is attached to an Attribute (RFC-4).
 *  Free property: TODO

### Goals

 The specification of Property aims to define a standard structure and type to use in any implementation of Tango.

Additionally, it aims to:

* Provide the initial information when one starts a Tango Device

* Be usable as keeping global variable in the Tango control system

* Provide meta data complementing any data in Tango


### Use Cases

There are many use cases for the usage of a Property:

* To connect a real network equipment a Property can define the target IP address

* To change the representation of an information in order to be more user friendly a Property can define a new data format for the graphical user interface to convert the raw data.

* TODO: Memorized Attribute


## Specification

A Tango Property is a strict definition of a pair of key/value
* The Property SHALL have one key, called Property Name
* The Property SHALL have one value, which could be empty, called Property Value
* If the device is started upon a Tango Database, Class, Device, Attribute and Free Properties MAY be persisted into the Tango Database (RFC6)
* If the device is started without a Tango Database, the device and class properties MAY be persisted in a file (but no support for the attribute properties) 
* All device and class properties MAY be directly modified and accessed through the database device or its file
* Attribute properties SHOULD be modified and accessed at any time through the device object (RFC-2)
* All default attribute and device properties are OPTIONAL
* The attribute, class and device properties MAY be defined in the device code and they MAY be OPTIONAL OR REQUIRED (mandatory)
* TODO memorized attributes, system properties
* TODO : properties history
* TODO: error management
* All device and class properties MUST be loaded at init command or device start-up
* If a device property does not exist, the device MUST load the class property having the same name
* All device and class properties MAY be modified and accessed through the database device when using a Tango Database (RFC-6)
* A Tango device MUST manage the following default device properties: cmd_min_poll_period, min_poll_period, attr_min_poll_period, poll_ring_depth, cmd_poll_ring_depth, attr_poll_ring_depth, polled_attr, logging_target, logging_level, logging_rft (TODO: description of each one)
* An attribute MUST manage the following default attribute properties (TODO: descriptions):
	 * String **label** default value "";
	 * String **description** default value  "No description"
	 * String **unit** default value "No unit"
	 * String **standard_unit** default value "No standard unit"
	 * String **display_unit** default value "No display unit"
	 * String **format** default value :
				* attribute type is a string, "%s"
				* attribute type is float or double, "%6.2f"
				* "Not specified" otherwise
  * String **min_value** default value "Not specified"
  * String **max_value** default value  "Not specified"
  * String **min_alarm** default value "Not specified"
  * String **max_alarm** default value  "Not specified"
  * String **min_warning** default value  "Not specified"
  * String **max_warning** default value  "Not specified"
  * String **delta_t** default value  "Not specified"
  * String **delta_val** default value "Not specified"
		* String **abs_change** default value "Not specified"
		* String **rel_change** default value "Not specified"
		* String **event_period** default value "Not specified"
		* String **archive_period** default value "Not specified"
		* String **archive_rel_change** default value "Not specified"
		* String **archive_abs_change** default value "Not specified"
		* String **enum_labels** default value "Not specified"
		* String **__root_att** default value "Not specified"
		* String **enum_labels**
		* String **__value**


### Naming convention
* The Property's Name SHALL use the following convention:
``` ABNF
property-name = 1*VCHAR
```
* The property name is case insensitive
* The Property value MUST use the following convention:
        single value that may contain any character
        or an array with carriage return
        special values: "NaN", "inf"
	* TODO nodbproperties file convention		
```
# --- 1/1/1 properties
1/1/1->myProp:titi


CLASS/MyClass->myClassProp: 10
```
