---
domain: rfc.tango-controls.org
shortname: 5/PROPERTY
name:Property
status: raw
editor: Gwenaëlle Abeillé (gwenaelle.abeille@synchrotron-soleil.fr)
---

This document describes the Property,  a Tango concept representing an element of configuration in order to customise a Tango element. This document describes version 1.0 of the Property.

See also: Y/OtherTemplate

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Property Specification

 A Property is a persisted item of Tango that is mainly used for configuration purposes. 4 types of properties exist:

 *  Class property: a property that is attached to a Device Class (cf RFC-2). This property is accessible from all devices of the class
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


## Specification

A Tango Property is a strict definition of a pair of key/value
* The Property SHALL have one key, called Property Name
* The Property SHALL have one value

### Naming convention
* The Property's Name SHALL use the following convention:
``` ABNF
property-name = 1*VCHAR
```
