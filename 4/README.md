---
domain: rfc.tango-controls.org
shortname: 4/Attribute
name:Attribute
status: draft
editor: [Sergi Blanchi-Torné](mailto:sblanch@cells.es)
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

In object oriented terminology, the Attribute corresponds to an instance
variable (also called a field or a member) of a Device object.
See [RFC 2/Device](/2) for the definition of the Device.

### Use Cases

Some example use cases of an Attribute are:
* an Attribute can represent a position of a motor device,
* an Attribute can represent a temperature of a thermocouple device.


## Specification

An Attribute has:
* A set of static metadata that constitute Attribute's definition,
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
  See `<attribute-name>` specification below,
* *data type*, an enumeration describing the type of the data (X/DataTypes),
* *data format*, an enumeration describing the dimension of the data
  (one of *SCALAR*, *SPECTRUM*, *IMAGE*),
* *writable*, an enumeration describing the write access to the Attribute
  (one of *READ*, *WRITE*, *READ_WRITE*, *READ_WITH_WRITE*).
  * An Attribute can be read from, if *writable* is one of *READ*, *READ_WRITE*,
    *READ_WITH_WRITE*.
  * An Attribute can be written to, if *writable* is one of *WRITE*, *READ_WRITE*.
* *display level*, an enumeration describing visibility level of the Attribute
  (one of *EXPERT*, *OPERATOR*).

> **Note:**
> Although it is possible to use a wide range of characters in the Attribute's
> name, it is RECOMMENDED to use only numbers, ASCII letters (upper- and lower-case)
> and an underscore (`_`) to ensure compatibility with various tools and client
> applications. Also note that the Attribute name SHOULD contain at least one
> letter and SHOULD NOT start with a digit.


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
  It MAY be empty.


#### Memorized attribute

An Attribute MUST have associated following static metadata:
* *memorized*, a flag describing whether *set value* is stored in the database
  and restored during Attribute initialization,
* *write hardware at init*, a flag describing whether the memorized
  *set value* is written to the Attribute during initialization or *init* call.
  It is effective only if *memorized* is also set.

A conforming implementation MUST allow to set the *memorized* flag and MUST
support memorization for attributes which:
* *data format* is *SCALAR*,
* *writable* is *WRITE* or *READ_WRITE*,
* *data type* is not *STATE* or *ENCODED*.

If *memorized* is set and if database is being used,
the *set value* MUST be persisted in the *__value* Attribute property upon
each write to the Attribute.

If *memorized* is set and *__value* Attribute property is other than
"Not used yet" (default):
* only during Attribute initialization (at startup):
  * *set value* MUST be set with a value stored in *__value* Attribute property,
* during Attribute initialization (at startup) or during an *init* command
  execution, if *write hardware at init* is also set:
  * value stored in *set value* MUST be written to the Attribute.

#### Forwarded attribute

An Attribute can be a forwarded attribute. A forwarded Attribute MUST have
associated *__root_att* Attribute property.
It MUST be defined before attribute initialization at startup.
It MUST NOT be changed in runtime.
It MUST point to an existing attribute in another device.

Allowed syntax for *__root_att* is specified below as `<full-attribute-name>`.

Each read and write request to a forwarded Attribute MUST be passed
to the Attribute pointed by *__root_att*.

Each change to properties or to other metadata associated with a forwarded
Attribute MUST be reflected in the Attribute pointed by *__root_att*.

Each change to properties or to other metadata associated with the Attribute
pointed by *__root_att* MUST be reflected in the forwarded Attribute.


### Attribute properties

An Attribute MAY have associated dynamic metadata in form of
Properties (see [RFC 5/Property](/5)).

General properties:
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
* *min value*, describing the minimum value of Attribute's *set value*.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be defined only for writable Attributes.
  If defined:
  it MUST be a valid numerical value,
  it MUST be lower than *max value* (if specified),
* *max value*, describing the maximum value of Attribute's *set value*.
  It MUST be defined only for Attributes with numerical *data type*.
  It MUST be defined only for writable Attributes.
  If defined:
  it MUST be a valid numerical value,
  it MUST be greater than *min value* (if specified).

Properties for alarming purposes:
* *min alarm*, describing the threshold value of Attribute's *read value*
  below which the attribute is considered as having alarm *quality*.
  It MUST be defined only for Attributes with numerical *data type*.
  If defined:
  it MUST be a valid numerical value,
  it MUST be lower than *max alarm* (if specified),
* max alarm*, describing the threshold value of Attribute's *read value*
  above which the attribute is considered as having alarm *quality*.
  It MUST be defined only for Attributes with numerical *data type*.
  If defined:
  it MUST be a valid numerical value,
  it MUST be greater than *min alarm* (if specified),
* *min warning*, is deprecated,
* *max warning*, is deprecated,
* *delta val*, describing the maximum difference between Attribute's
  *read value* and *set value* after *delta t* time period,
  above which the attribute is considered as having alarm *quality*.
  It MUST be defined only for writable Attributes,
* *delta t*, describing the time period (in milliseconds) after which the
  difference between *read value* and *set value* must be lower than
  *delta val*. Otherwise the attribute is considered as having alarm *quality*.
  It MUST be defined only for writable Attributes,
  It MUST be defined if *delta val* is also defined.

Properties for event reporting purposes:
* *rel change*, *abs change*, *archive rel change*, *archive abs change*,
  describing the threshold values for relative and absolute change
  in Attribute's *read value* above which
  a *CHANGE* event or *ARCHIVE* event MUST be reported.
  It MUST be either a valid numerical value
  or a pair of valid numerical values separated by a `,`.
  If one value is specified, it MUST be used for both negative
  and positive change.
  If two values are specified, the first MUST be used for negative change
  and the second MUST be used for positive change.
  It MUST be defined only for Attributes with numerical *data type*,
* *period*, *archive period*, describing the minimum time period
  in milliseconds after which a *PERIODIC* or *ARCHIVE* event MUST be reported,
  regardless of Attribute's *read value*. If *period* is not specified,
  a default value of 1000 ms is used,

If relative change is specified, it MUST be expressed as a percentage change
relative to the value reported in the previous event, e.g. a relative change
of 1 will generate an event when:
```
(current value - previous value) / previous value > 1%
```

See [Attribute events section](#attribute-events) for more details.

```abnf
rel-change = change
abs-change = change

archive-rel-change = change
archive-abs-change = change

number = [ "-" ] 1*DIGIT [ "." ] *DIGIT
change = number [ "," number ]

period = 1*DIGIT
archive-period = period
```

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

A *full attribute name* consists of *device name* and *attribute name*
separated by "/" character.

See [RFC 2/Device](/2) for the definition of `<device-name>`.

An Attribute MAY have associated *alias*, a string which can be used
in place of *full attribute name* to address the Attribute.
An Attribute MUST NOT have more than one *alias*.

An *alias* MUST be stored in the database.

An *alias* MUST be unique across the whole Tango system.

An *alias* MAY contain any character except
that it MUST NOT contain any of `/`, ` ` (space), `#`, `:`, `->`.
It MAY be empty.
See `<attribute-alias>` specification below.


### Attribute events

An Attribute can send following events:
* *PERIODIC*,
* *CHANGE*,
* *ARCHIVE*,
* *DATA READY*,
* *ATTR CONF*,
* *USER*,

Events can be sent automatically by a polling mechanism,
automatically by the Tango system or manually from the device server code.

The polling mechanism can send PERIODIC, CHANGE and ARCHIVE events.
In order to send events via the polling mechanism,
the polling for the Attribute MUST be enabled.
See [Attribute properties](#attribute-properties) section for conditions upon
which the events are sent with the polling enabled.
See [RFC XX/Cache system](/XX) for more details on polling configuration.

The Tango system MUST send ATTR CONF event whenever Attribute configuration
is changed.
See [Attribute properties](#attribute-properties) section for a list of
modifiable attribute Properties.

The device server code can manually send *CHANGE*, *ARCHIVE*, *DATA READY*
and *USER* events without the need to configure the polling for the Attribute.


### Attribute naming schema

Formal specification of Attribute name is given below:
```ABNF
attribute-name = *attribute-name-char
attribute-name-char = %d48-57 / %d65-90 / %d97-122 / "_" ; 0-9 / A-Z / a-z / _

full-attribute-name = device-name "/" attribute-name

attribute-alias = *attribute-alias-char
attribute-alias-char = OCTET ; except %x00 "/" " " "#" ":" "->"
```
