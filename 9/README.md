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

``` ABNF
 DataType = DevVoid 
 DataType =/ DevBoolean 
 DataType =/ DevFloat / DevDouble 
 DataType =/ DevShort / DevLong / DevLong64
 DataType =/ DevUChar / DevUShort / DevULong / DevULong64
 DataType =/ DevVarBooleanArray / DevVarDoubleArray / DevVarFloatArray
 DataType =/ DevString 
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

```ABNF
DevBoolean = "DEVBOOLEAN" | "DevBoolean"
```

* Values which DataType is DevBoolean SHALL be mapped to dev-boolean-value specification:

```abnf
dev-boolen-value = BIT
```

#### Integer data types

DevShort, DevLong, DevLong64 represents singed integer values. DevUChar, DevUShort, DevULong and DevULong64 represents 
unsigned values.

```abnf
 DevShort = "DEVSHORT" | "DevShort"
 DevLong = "DEVLONG" | "DevLong"
 DevLong64 = "DEVLONG64" | "DevLong64"
```

* Values of DevShort, DevLong, DevLong64, DevUChar, DevUShort, DevULong and DevULong64 types SHALL have a text 
 representation dev-short-value, dev-long-value, dev-long64-value, dev-uchar-value, dev-ushort-value, dev-ulong-value, 
 dev-ulong64-value respectively as follows:
  
```abnf
 dev-short-value = ["-"] DIGIT* ; In the range of "(2^15) - 1" to "-(2^15)".
 dev-long-value = ["-"] DIGIT* ; In the range of "(2^31) - 1" to "-(2^31)".
 dev-long64-value = ["-"] DIGIT* ; In the range of "(2^63) - 1" to "-(2^63)".
 ...
```

* Values of integer types are sumaDevShort typ

| DataType | Range | Bits of information |
-----------|-------|---------------------|
| DevShort | -32768 to 32767 |         16 |
| DevLong  | -2147483648 to -2147483647 | 32 |
| DevLong64  | -18446744073709551616 to 18446744073709551615 | 64 |


#### Floating point data types

Values of types DevFloat and DevDouble are  




