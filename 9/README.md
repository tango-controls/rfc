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

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public 
License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later 
version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even 
the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more
details. You should have received a copy of the GNU General Public License along with this program; if not, 
see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by 
the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", 
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

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

### DataType

* Any value of Properties, Attributes, Pipes' fields and Commands' input and output arguments SHALL have defined its 
  DataType.
  
* DataType SHALL be as follows: 

```abnf
 DataType = DevVoid 

 DataType =/ DevBoolean 
 
 DataType =/ DevShort / DevLong / DevLong64
 DataType =/ DevUChar / DevUShort / DevULong / DevULong64
 
 DataType =/ DevFloat / DevDouble

 DataType =/ DevString  
 
 DataType =/ DevVarBooleanArray / DevVarDoubleArray / DevVarFloatArray
 DataType =/ DevVarShortArray / DevVarLongArray / DevVarLong64Array / DevVarCharArray
 DataType =/ DevVarStringArray
 DataType =/ DevVarUShortArray / DevVarULongArray / DevVarULong64Array
 
 DataType =/ DevEncoded / DevVarEncodedArray
 
 DataType =/ DevVarLongStringArray / DevVarDoubleStringArray
 
 DataType =/ DevState
  
``` 



### Numeric

#### DevBoolean

DevBoolean is a data type which values represents logical state of true or false.

* DevBoolean SHALL be defined as follows:

```abnf
DevBoolean = "DEVBOOLEAN" | "DevBoolean"
```

* Values which DataType is DevBoolean SHALL carry one bit of information.

#### Integer data types

DevShort, DevLong, DevLong64 represent singed integer values. DevUChar, DevUShort, DevULong and DevULong64 represent
unsigned values.

* Integer DataType SHALL have the following names:

```abnf
 DevShort = "DEVSHORT" | "DevShort"
 DevLong = "DEVLONG" | "DevLong"
 DevLong64 = "DEVLONG64" | "DevLong64"
 DevUChar = "DEVUCHAR" | "DevUChar"
 DevUShort = "DEVUSHORT" | "DevUShort"
 DevULong = "DEVULONG" | "DevULong"
 DevULong64 = "DEVULONG64" | "DevULong64"
```

* Values of integer data types SHALL be in defined ranges and the information it cary SHALL have number of bits
  specified in the table below:

| DataType | Range | Bits of information |
-----------|-------|---------------------|
| DevShort | -(2^15) to (2^15)-1 |         16 |
| DevLong  | -(2^31) to (2^31)-1 | 32 |
| DevLong64  | -(2^63) to (2^63)-1 | 64 |
| DevUChar | 0 to 255 | 8 |
| DevUShort | 0 to (2^16)-1 | 16 |
| DevULong | 0 to (2^32)-1 | 32 |
| DevULong64 | 0 to (2^64)-1 | 64 |

#### Floating point data types

DevFloat and DevDouble represent floating point values.

* DevFloat and DevDouble SHALL use the following names:

```abnf
 DevFloat = "DEVFLOAT" | "DevFloat"
 DevDouble = "DEVDOUBLE" | "DevDouble"
```

* Values of DevFloat type SHALL be of IEEE 754 single precision floating-point format and MAY be encoded in 32 bits.
* Values of DevDouble type SHALL be of IEEE 754 double precision floating-point format and MAY be encoded in 64 bits.

#### String data type

DevString values represents strings (sequence of characters).

* DevString SHALL use the following names:

```abnf
 DevString = "DEVSTRING" / "DevString"
```

* Values of DevString SHALL be according to the string-value rule defined as follows:

```abnf
 string-value = *CHAR
``` 




