---
domain: rfc.tango-controls.org
shortname: 9/DataTypes
name: Data types
status: raw
editor: 
contributors: [Piotr Goryl](mailto:piotr.goryl@s2innovation.com)

---

This document describes Tango Controls Data Types specification version 5.0.

See also: Y/OtherTemplate

## Preamble

Copyright (c) 2019 Tango Controls Community.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Data Types Specification

Data Types are specifying format and limits of pieces of information exchanged between Tango Controls components.   

### Goals

Data Types aims to standardise a way how different kind of information is entered, displayed and processed within
a Tango Controls systems irrespectively to its encoding in a transport protocol or computers' CPU architecture.
It also allows to distinguish different kind of numeric data, string data, enumerations or multi-values data. 

### Use Cases

There are many use cases for Data Types. Some of them are listed below:

* To specify expected data format for attributes, commands and pipes.

* To display in the same way a numeric values read from a devices when some of them are using big-endian encoding and 
  other use little-endian encoding.

* To limit information processed to a practical level by choosing a Data Type of certain precision.
  For example, a value of Data Type DevShort typically provides less precise information and consumes less computing 
  resources than a value of Data Type DevLong.


### Numerics


### Strings


### Enumerations


### Exceptions


### Multi-values data



