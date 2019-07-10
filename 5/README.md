---
domain: rfc.tango-controls.org
shortname: 5/PROPERTY
name:Property
status: raw
editor: Vincent Hardion (vincent.hardion@maxiv.lu.se)
---

This document describes the Property,  a Tango concept representing an element of configuration in order to customise a Tango element. This document describes version 1.0 of the Property.

See also: Y/OtherTemplate

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Property Specification

 A Property is designed to represent any information of configuration in Tango.

### Goals

A Property is a persisted item of Tango that is mainly used for configuration purposes. 4 types of properties exist:

  *  Class property: a property that is attached to a Device Class (cf RFC-2). This property is accessible from all devices of the class
  * Device property: a property that is attached to a Device (RFC-2). This property is accessible from only one device
  *  Attribute property: a property that is attached to an Attribute (RFC-4).
  * Free property: TODO





### Use Cases

There are many use cases for the usage of a Property:

* To connect a real network equipment a Property can define the target IP address

* To change the representation of an information in order to be more user friendly a Property can define a new data format for the graphical user interface to convert the raw data.
* Memorized Attribute?


## Specification

A Tango Property is a strict definition of a pair of key/value

    configuration are represented in the form of properties.
    data are represented in the form of attributes.
    actions are represented in the form of commands.

Its model can be represented as a defined tree which each elements are from a defined types: Class, Device, Property, Attribute, Command following the rules below:

A Tango Property is a strict definition of a pair of key/value

 *   All properties are OPTIONAL
 *   The Property SHALL have one key, called Property Name
  *  The Property SHALL have one value
 *   All device and class properties SHOULD be modified and accessed througth the database device (RFC-6)
 *   All device and class properties SHALL be loaded at init command or device start-up
 *   Attribute properties SHOULD be modified and accessed at any time througth the device object (RFC-2)
 *   If a device property does not exists, the device SHALL load its class property
  *  A Tango device MAY use the following default properties: cmd_min_poll_period, min_poll_period, attr_min_poll_period, poll_ring_depth, cmd_poll_ring_depth, attr_poll_ring_depth, polled_attr, logging_target, logging_level, logging_rft (TODO: description of each one)
  *  Some attribute properties are defined in the kernel and are OPTIONAL:

   **         String label default value "";
   **         String description default value  "No description"
   **        String unit default value "No unit"
   **       String standardUnit default value "No standard unit"
   **          String displayUnit default value "No display unit"
   **         String format default value :
   **           attribute type string "%s"
    **          attribute type float or double "%6.2f"

            "Not specified" otherwise

            String minValue default value "Not specified"
            String maxValue default value  "Not specified"
            String minAlarm default value "Not specified"
            String maxAlarm default value  "Not specified"
            String minWarning default value  "Not specified"
            maxWarning default value  "Not specified"
            String deltaT default value  "Not specified"
            String deltaVal default value "Not specified"
            String[] alarmExtensions default value new String[0];
            String[] extensions default value new String[0];
            String[] sysExtensions default value new String[0];
            double deltaValDouble default value 0;
            String[] enumLabels default value new String[]{Constants.NOT_SPECIFIED};
            String rootAttribute default value Constants.NOT_SPECIFIED;

### Naming convention
* The Property's Name SHALL use the following convention:
``` ABNF
property-name = 1*VCHAR
```
*     The Property value MUST use the following convention:
       ** single value that may contains any caracter
      **  or an array with carriage return
