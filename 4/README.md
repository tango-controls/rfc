---
domain: rfc.tango-controls.org
shortname: 4/Attribute
name:Attribute
status: draft
editor: [Sergi Blanchi-Torné](mailto:)
contributors: [Michal Liszcz](mailto:michal.liszcz@s2innovation.com)
---

This document describes the Tango Attribute model specification version 5.0.


## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).


## Tango Attribute specification

This specification is intended to formally document the Tango Attribute static
model. Runtime representation of Attribute in conforming Tango implementations
MUST follow this specification.


### Goals

An Attribute is a Tango concept representing a physical quantity of a device.
The main purpose of an Attribute is to provide read and (optionally) write
access to this quantity.


### Use Cases

Some example use cases of an Attribute are:
* an Attribute can represent a position of a motor device,
* an Attribute can represent a temperature of a thermocouple device.


## Specification

An Attribute has:
* A set of static metadata that constitute to Attribute's definition,
* A set of dynamically configurable properties, e.g. for alarming purposes,
* A set of runtime parameters describing Attribute's value at given point
  in time.

### Attribute definition

The static metadata is part of the Attribute definition. It MUST be defined
before an Attribute is created by any Tango implementation and MUST NOT change
at runtime after the Attribute is initialized.

An Attribute MUST have associated following static metadata:
* *name*, a string identifying the Attribute.
  It MUST be unique [^1] among all Attributes of a particular device.
  It MAY contain any character (including multi-byte characters)
  except that it MUST NOT contain `\0` character.
  It MAY be empty,
* *data type*, an enumeration describing the type of the data (X/DataTypes),
* *data format*, an enumeration describing the dimension of the data
  (one of *SCALAR*, *SPECTRUM*, *IMAGE*),
* *writable*, an enumeration describing the write access to the Attribute
  (one of *READ*, *WRITE*, *READ_WRITE*, *READ_WITH_WRITE*).
* *display level*, an enumeration describing visibility level of the Attribute
  (one of *EXPERT*, *OPERATOR*).

An Attribute can be read from, if *writable* is one of *READ*, *READ_WRITE*,
*READ_WITH_WRITE*.

An Attribute can be written to, if *writable* is one of *WRITE*, *READ_WRITE*.

[^1]: In current implementation it is possible to define more than one
Attribute with the same name, but only one can be accessed externally.

If *data format* is *SPECTRUM*,
an Attribute MUST have associated following additional static metadata:
* *max dim x*, an integer describing the maximum allowed number of data
  elements.
  The Attribute MUST be able to store up to *max dim x* data elements.
  It MUST be greater than 0.

If *data format* is *IMAGE*,
an Attribute MUST have associated following additional static metadata:
* *max dim x*, an integer describing the maximum allowed number of data
  elements in dimension X.
  The Attribute MUST be able to store up to *max dim x* data elements
  in the X dimension.
  It MUST be greater than 0,
* *max dim y*, an integer describing the maximum allowed number of data
  elements in dimension Y.
  The Attribute MUST be able to store up to *max dim y* data elements
  in the Y dimension.
  It MUST be greater than 0.

If *writable* is *READ_WITH_WRITE*,
an Attribute MUST have associated following additional static metadata:
* *writable attribute name*, a string describing the *name* of associated
  writable attribute.
  It MUST point to an attribute.
  The Attribute pointed to MUST have *writable* set to one of *WRITE*,
  *READ_WRITE*.

If *data type* is *ENUM*,
an Attribute MAY have associated following additional static metadata:
* *enum labels*, a list of strings describing textual representation
  of enumerated values.
  Its elements MAY contain any character (including multi-byte characters)
  except that they MUST NOT contain `\0` character.
  It MAY be empty.

TODO
These are associated with the device in DB but impact the attribute.
TBD where we want to document such properties.
* *polled*,
* *polling period*.


#### Memorized attribute

An Attribute MUST have associated following static metadata:
* *memorized*, a flag describing whether *set value* is stored in the database
  and restored during Attribute initialization,
* *write hardware at init*, a flag describing whether *set value* is
  written to the Attribute during initialization or *init* call.
  It is effective only if *memorized* is also set.

To set *memorized*, following constraints MUST be satisfied:
* *data format* MUST be *SCALAR*,
* *data type* MUST NOT be *STATE* or *ENCODED*,
* *writable* MUST NOT be *READ* or *READ_WITH_WRITE*.

If *memorized* is set and if database is being used,
the *set value* MUST be persisted in the *__value* property upon each write
to the Attribute.

If *memorized* is set and *__value* property is other than "Not used yet"
(default):
* only during Attribute initialization (at startup):
  * *set value* MUST be set with a value stored in *__value* property,
* during Attribute initialization (at startup) or during an *init* command
  execution, if *write hardware at init* is also set:
  * value stored in *set value* MUST be written to the Attribute.

#### Forwarded attribute

TODO


### Attribute properties

An Attribute MAY have associated following dynamic metadata in form of
Properties (see RFC 5/Property):
* *description*, providing textual information about the Attribute,
* *label*, providing textual label to use as Attribute's name,
* *unit*, providing textual representation of Attribute's unit,
* *standard unit*, describing the conversion factor to transform Attribute's
  value into S.I units. It MUST be a valid numerical value,
* *display unit*, describing the conversion factor to transform Attribute's
  value into value usable in GUIs. It MUST be a valid numerical value,
* *format*, describing how to convert Attribute's data to a textual
  representation. It MUST comply to
  [`printf`](http://man7.org/linux/man-pages/man3/printf.3.html)
  format string specification,
* *min value*, describing the minimum value of Attribute's *read value*.
  It MUST be a vaild numerical value.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be defined only for writable Attributes.
  It MUST be lower than *max value*,
* *max value*, describing the maximum value of Attribute's *read value*.
  It MUST be a vaild numerical value.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be defined only for writable Attributes.
  It MUST be greater than *max value*,
* *min alarm*, describing the threshold value of Attribute's *read value*
  below which the attribute is considered as having alarm *quality*.
  It MUST be a vaild numerical value.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be lower than *max alarm*,
* max alarm*, describing the threshold value of Attribute's *read value*
  above which the attribute is considered as having alarm *quality*.
  It MUST be a vaild numerical value.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be greater than *min alarm*,
* *min warning*, is deprecated,
* *max warning*, is deprecated,
* *delta val*, describing the maximum difference between Attribute's
  *read value* and *set value* after *delta t* time period,
  above which the attribute is considered as having alarm *quality*.
  It MUST be defined only for writable Attributes,
* *delta t*, describing the time period after which the difference between
  *read value* and *set value* must be lower than *delta val*.
  Otherwise the attribute is considered as having alarm *quality*.
  It MUST be defined only for writable Attributes,
  It MUST be defined if *delta val* is also defined.
* TODO: describe event-related properties,
* *rel change*,
* *abs change*,
* *period*, 
* *archive rel change*,
* *archive abs change*,
* *archive period*,


### Attribute runtime parameters

At given point in time an Attribute MUST have associated:
* *quality*, an enumeration describing the state *read value*
  (one of *VALID*, *INVALID*, *ALARM*, *CHANGING*, *WARNING*),
* *read value*, an object representing the value of the Attribute.
  It MUST conform to *data format* and *data type*,
* *read dim x*, an integer describing the number of data elements in
  *read value* in X dimension.
  If *data format* is *SCALAR*, it MUST be either 0 or 1.
  If *data format* is *SPECTRUM* or *IMAGE*, it MUST be between 0 and
  *max dim x*.
* *read dim y*, an integer describing the number of data elements in
  *read value* in Y dimension.
  If *data format* is *SCALAR* or *SPECTRUM*, it MUST be 0.
  If *data format* is *IMAGE*, it MUST be between 0 and *max dim y*.

Additionally, if *writable* is *WRITE* or *READ_WRITE*,
at given point in time an Attribute MUST have associated:
* *set value*, an object representing the value set to the Attribute.
  It MUST conform to *data format* and *data type*,
* *write dim x*, an integer describing the number of data elements in
  *set value* in X dimension,
* *write dim y*, an integer describing the number of data elements in
  *set value* in Y dimension.


### Attribute aliases

TODO
An Attribute MAY have associated *aliases*.


### TODO

Formal specification of Attribute static model is given below:
```ABNF
attribute = "whatever"
```
