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

### DataType

Data Type is association of Data Type name and related value format

``` ABNF
 DataType = dev-void | dev-double | dev-boolean | dev-float | dev-short | dev-long | dev-long64 | dev-string | dev-uchar | dev-ushort | dev-ulong | dev-ulong64 | dev-boolean-array | dev-double-array | dev-float-array | dev-short-array | dev-long-array | dev-long64-array | dev-char-array | dev-string-array | dev-ushort-array | dev-ulong-array | dev-ulong64-array | dev-long-string-array | dev-double-string-array | dev-boolean-array | dev-encoded | dev-encoded-array 
``` 

### Numeric


``` ABNF
 
 argument-desc = 0*CHAR

 ; Float Definition inspired by python float definition 
 ; Some examples of floating point literals: 3.14 10. .001 1e100 3.14e-10
 floatnumber = [ "-" ] pointfloat | exponentfloat
 pointfloat = [intpart] fraction | intpart "."
 exponentfloat:  ( %d1-9 DIGIT* | pointfloat) exponent
 intpart:        %d1-9 DIGIT* | %d0
 fraction:       "." DIGIT+
 exponent:       ("e"|"E") ["+"|"-"] DIGIT+

 
 dev-boolean = dev-boolean-type WSP dev-boolean-value
 dev-boolean-type = "DEVBOOLEAN" | "DevBoolean"
 dev-boolean-value = BIT
 
 dev-double = dev-double-type WSP dev-double-value
 dev-double-type = "DEVDOUBLE" | "DevDouble"
 dev-double-value = floatnumber ; double-precision normalized numbers in IEC 60559

 

 dev-float = dev-float-type WSP dev-float-value
 dev-float-type = "DEVFLOAT" | "DevFloat"
 dev-float-value = floatnumber ; single-precision normalized numbers in IEC 60559

 dev-short = dev-short-type WSP dev-short-value
 dev-short-type = "DEVSHORT" | "DevShort"
 dev-short-value = ["-"] DIGIT* ; In the range of "(2^15) - 1" to "-(2^15)".

 dev-long = dev-long-type WSP dev-long-value
 dev-long-type = "DEVLONG" | "DevLong"
 dev-long-value = ["-"] DIGIT* ; In the range of "(2^31) - 1" to "-(2^31)".

 dev-long64 = dev-long64-type WSP dev-long64-value
 dev-long64-type = "DEVLONG64" | "DevLong64"
 dev-long64-value = ["-"] DIGIT* ; In the range of "(2^63) - 1" to "-(2^63)".



 dev-uchar = dev-uchar-type WSP dev-uchar-value
 dev-uchar-type = "DEVUCHAR" | "DevUChar"
 dev-uchar-value = DIGIT* ; In the range of "0" to "(2^8) - 1". In abnf syntax it may correspond to %d0-255

 dev-ushort = dev-ushort-type WSP dev-ushort-value
 dev-ushort-type = "DEVUSHORT" | "DevUShort"
 dev-ushort-value = DIGIT* ; In the range of "0" to "(2^16) - 1".

 dev-ulong = dev-ulong-type WSP dev-ulong-value
 dev-ulong-type = "DEVULONG" | "DevULong"
 dev-ulong-value = DIGIT* ; In the range of "0" to "(2^32) - 1".

 dev-ulong64 = dev-ulong64-type WSP dev-ulong64-value
 dev-ulong64-type = "DEVULONG64" | "DevULong64"
 dev-ulong64-value = DIGIT* ; In the range of "0" to "(2^64) - 1".

 dev-state = dev-state-type WSP dev-state-value
 dev-state-type = "DEVSTATE" | "DevState"
 dev-state-value = "ALARM" | "INSERT" | "STANDBY" | "CLOSE" | "MOVING" | "UNKNOWN" | "DISABLE" | "OFF" | "EXTRACT" | "ON" | "FAULT" | "OPEN" | "INIT" | "RUNNING"

 dev-encoded = dev-encoded-type WSP dev-encoded-value
 dev-encoded-type = "DEVENCODED | "DevEncoded"
 dev-encoded-value =  dev-encoded-encoded-format  dev-encoded-encoded-data
 dev-encoded-encoded-format = dev-string
 dev-encoded-encoded-data = dev-char-array

 
``` 


### Strings
``` ABNF
 dev-string = dev-string-type WSP dev-string-value
 dev-string-type = "DEVSTRING" | "DevString"
 dev-string-value = DQUOTE CHAR* DQUOTE

```

### Enumerations

* Device state SHALL be represented by `dev-state` Data Type defined as follows:
 
```abnf
 dev-state = dev-state-type WSP dev-state-value
 dev-state-type = "DEVSTATE" | "DevState"
 dev-state-value = "ALARM" | "INSERT" | "STANDBY" | "CLOSE" | "MOVING" | "UNKNOWN" | "DISABLE" | "OFF" | "EXTRACT" | "ON" | "FAULT" | "OPEN" | "INIT" | "RUNNING"
```



### Exceptions


### Multi-values data

```abnf
 dev-boolean-array = dev-boolean-array-type  WSP dev-boolean-array-value
 dev-boolean-array-type = "DEVVARBOOLEANARRAY" | "DevVarBooleanArray"
 dev-boolean-array-value = "[" dev-boolean [ ("," dev-boolean)* ] "]" 

 dev-double-array = dev-double-array-type  WSP dev-double-array-value
 dev-double-array-type = "DEVVARDOUBLEARRAY" | "DevVarDoubleArray"
 dev-double-array-value = "[" dev-double [ ("," dev-double)* ] "]" 

 dev-float-array = dev-float-array-type WSP dev-float-array-value
 dev-float-array-type = "DEVVARFLOATARRAY" | "DevVarFloatArray"
 dev-float-array-value = "[" dev-float [ ("," dev-float)* ] "]"

 dev-short-array = dev-short-array-type WSP dev-short-array-value
 dev-short-array-type = "DEVVARSHORTARRAY" | "DevVarShortArray"
 dev-short-array-value = "[" dev-short [ ("," dev-short)* ] "]"

 dev-long-array = dev-long-array-type WSP dev-long-array-value
 dev-long-array-type = "DEVVARLONGARRAY" | "DevVarLongArray"
 dev-long-array-value = "[" dev-long [ ("," dev-long)* ] "]"

 dev-long64-array = dev-long64-array-type WSP dev-long64-array-value
 dev-long64-array-type = "DEVVARLONG64ARRAY" | "DevVarLong64Array"
 dev-long64-array-value = "[" dev-long64 [ ("," dev-long64)* ] "]"

 dev-char-array = dev-char-array-type WSP dev-char-array-value
 dev-char-array-type = "DEVVARCHARARRAY" | "DevVarCharArray"
 dev-char-array-value = "[" dev-char [ ("," dev-char)* ] "]"

 dev-string-array = dev-string-array-type WSP dev-string-array-value
 dev-string-array-type = "DEVVARSTRINGARRAY" | "DevVarStringArray"
 dev-string-array-value = "[" dev-string [ ("," dev-string)* ] "]"

 dev-ushort-array = dev-ushort-array-type WSP dev-ushort-array-value
 dev-ushort-array-type = "DEVVARUSHORTARRAY" | "DevVarUShortArray"
 dev-ushort-array-value = "[" dev-ushort [ ("," dev-ushort)* ] "]"

 dev-ulong-array = dev-ulong-array-type WSP dev-ulong-array-value
 dev-ulong-array-type = "DEVVARULONGARRAY" | "DevVarULongArray"
 dev-ulong-array-value = "[" dev-ulong [ ("," dev-ulong)* ] "]"

 dev-ulong64-array = dev-ulong64-array-type WSP dev-ulong64-array-value
 dev-ulong64-array-type = "DEVVARULONG64ARRAY" | "DevVarULong64Array"
 dev-ulong64-array-value = "[" dev-ulong64 [ ("," dev-ulong64)* ] "]" 

 dev-long-string-array = dev-long-string-array-type WSP dev-long-string-array-value
 dev-long-string-array-type = "DEVVARLONGSTRINGARRAY" | "DevVarLongStringArray"
 dev-long-string-array-value =  dev-long-string-array-lvalue  dev-long-string-array-rvalue
 dev-long-string-array-lvalue = dev-long-array
 dev-long-string-array-rvalue = dev-string-array

 dev-double-string-array = dev-double-string-array-type WSP dev-double-string-array-value
 dev-double-string-array-type = "DEVVARDOUBLESTRINGARRAY" | "DevVarDoubleStringArray"
 dev-double-string-array-value =  dev-double-string-array-dvalue dev-double-string-array-rvalue
 dev-double-string-array-dvalue = dev-double-array
 dev-double-string-array-rvalue = dev-string-array

 dev-encoded-array = dev-encoded-array-type WSP dev-encoded-array-value
 dev-encoded-array-type = DEVVARENCODEDARRAY | DevVarEncodedArray
 dev-encoded-array-value = "[" dev-encoded [ ("," dev-encoded)* ] "]"

```



