---
domain: rfc.tango-controls.org
shortname: 5/PROPERTY
name: Property
status: raw
editor: Gwenaëlle Abeillé <gwenaelle.abeille@synchrotron-soleil.fr>
contributors:
  - Reynald Bourtembourg <reynald.bourtembourg@esrf.fr>
  - Thomas Braun <thomas.braun@byte-physics.de>
  - Andy Gotz <andy.gotz@esrf.fr>
  - Vincent Hardion <vincent.hardion@maxiv.lu.se> 
  - Lorenzo Pivetta <lorenzo.pivetta@elettra.eu>git sgit 
---

This document describes the Tango Property model.

See also: 1/TANGO, 2/Device, 4/Attribute, 6/Database, 9/DataTypes

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Property Specification

This specification is intended to formally document the Tango Property model.
Runtime representation of Property in conforming Tango implementations MUST follow this specification.

### Goals

A Property represents a configuration parameter in Tango.

### Use Cases

Here are some Property use cases:

* To connect a real network equipment a Property can define the target IP address or hostname
* To change the representation of an information in order to be more user friendly a Property can define a new data format for the graphical user interface to convert the raw data
* To configure some alarms on Attribute Value (See RFC-4)
* To store the Attribute value of a Memorized Attribute (See RFC-4)
* To configure the labels associated to the possible values of a DevEnum Attribute
* ...

## Specification

A Property is a strict definition of a pair of key/value:
  * The Property SHALL have one key, called Property Name
  * The Property SHALL have one value, which could be empty, called Property Value

There are 4 kinds of Properties:

 *  Class Property: a Property related to a Device Class (RFC-2).
 *  Device Property: a Property related to a Device (RFC-2).
 *  Attribute Property: a Property related to an Attribute (RFC-4).
 *  Free Property: a Property defined for the entire Control System (RFC-1) i.e not related to a specific Class, Device or Attribute.

A Property MAY be persisted in the Tango Database (See RFC-1 and RFC-6) or into a file if no Tango Database is used.

Device Properties and Class Properties MAY have a _Default Value_ which is used if the Property Value has not been persisted.

A Tango Device Property MAY be defined as _Mandatory in Database_. 
This metadata can be used when the device server programmer requires this Property to be persisted.
A Device Property defined as _Mandatory in Database_ SHALL not have a Default Value.

Device Property and Class Property MAY have a Type metadata.
It is RECOMMENDED to support the following Types for Device Properties and Class Properties (See RFC-9):
  * DevBoolean
  * DevShort
  * DevUShort
  * DevLong
  * DevULong
  * DevLong64
  * DevULong64
  * DevFloat
  * DevDouble
  * DevString
  * DevVarShortArray
  * DevVarLongArray
  * DevVarLong64Array
  * DevVarFloatArray
  * DevVarDoubleArray
  * DevVarStringArray

It is RECOMMENDED to provide a way to retrieve a persisted Property Value and to convert it to the above types.

It is RECOMMENDED to support _Not a Number (NaN)_ as well as _-infinity (-inf)_ and _+infinity (+inf)_ DevFloat and DevDouble values.

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
