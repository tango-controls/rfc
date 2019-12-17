---
domain: rfc.tango-controls.org
shortname: 9/DataTypes
name: Data types
status: raw
editor: [Gwenaëlle Abeillé](mailto:gwenaelle.abeille@synchrotron-soleil.fr) 
contributors: [Piotr Goryl](mailto:piotr.goryl@s2innovation.com)

---

This document describes Tango Controls Data Types specification version 5.0.

See also: [1/TANGO](/1), [3/Command](/3), [4/Attribute](/4), [5/Property](/5), [6/Database](/6), [7/Pipe](/7), 
          

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
 
 DataType =/ DevState / DevVarStateArray

 DataType =/ DevEnum
  
 DataType =/ DevPipeBlob
 
``` 



### Numeric

#### DevBoolean

DevBoolean is a data type which values represents logical state of true or false.

* DevBoolean SHALL be defined as follows:

```abnf
DevBoolean = "DevBoolean"
```

* Values which DataType is DevBoolean SHALL carry one bit of information.

#### Integer data types

DevShort, DevLong, DevLong64 represent singed integer values. DevUChar, DevUShort, DevULong and DevULong64 represent
unsigned values.

* Integer DataType SHALL have the following names:

```abnf
 DevShort = "DevShort"
 DevLong = "DevLong"
 DevLong64 = "DevLong64"
 DevUChar = "DevUChar"
 DevUShort = "DevUShort"
 DevULong = "DevULong"
 DevULong64 = "DevULong64"
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
 DevFloat = "DevFloat"
 DevDouble = "DevDouble"
```

* Values of DevFloat type SHALL be of IEEE 754 single precision floating-point format and MAY be encoded in 32 bits.
* Values of DevDouble type SHALL be of IEEE 754 double precision floating-point format and MAY be encoded in 64 bits.

#### String data type

DevString values represents strings (sequence of characters).

* DevString SHALL use the following names:

```abnf
 DevString = "DevString"
```

* Values of DevString SHALL be according to the string-value rule defined as follows:

```abnf
 string-value = *CHAR
``` 

#### Sequence data types

DevVarBooleanArray,  DevVarShortArray, DevVarLongArray, DevVarLong64Array,
DevVarCharArray, DevVarUShortArray, DevVarULongArray, DevVarULong64Array, DevVarFloatArray, DevVarDoubleArray, 
DevVarStringArray, DevVarEncodedArray, DevVarStateArray are Sequence DataTypes. Sequence data types are sequences (arrays) of 
values (elements) of matching DataType, which are respectively, DevBoolean,  DevShort, DevLong, DevLong64, DevUChar, 
DevUShort, DevULong, DevULong64, DevFloat, DevDouble, DevString, DevEncoded, DevState.

* Names of the sequence data types SHALL be as follows:
```abnf
 DevVarBooleanArray = "DevVarBooleanArray"
 DevVarShortArray = "DevVarShortArray"
 DevVarLongArray = "DevVarLongArray"
 DevVarLong64Array = "DevVarLong64Array"
 DevVarCharArray = "DevVarCharArray"
 DevVarUShortArray = "DevVarUShortArray"
 DevVarULongArray = "DevVarULongArray"
 DevVarULong64Array = "DevVarULong64Array"
 DevVarFloatArray = "DevVarFloatArray"
 DevVarDoubleArray = "DevVarDoubleArray"
 DevVarStringArray = "DevVarStringArray"
 DevVarEncodedArray = "DevVarEncodedArray"
 DevVarStateArray = "DevVarStateArray"
```



* Value of sequence DataType SHALL follow <sequence-value> rule:
```abnf
 sequence-value = *element
```
Where \<element\> follows specification of value of the matching \<DataType\>

* Sequence DataType SHALL provide an interface to determine number of its elements.
* Sequence DataType SHALL provide an interface to index its elements.

#### Structures

Value of the DevEncoded type is a structure to carry binary data with application and context specific meaning. 

* Name of DevEncoded type SHALL be as follows:
```abnf
 DevEncoded = "DevEncoded"
```

* Value of \<DevEncoded\> SHALL be according to the \<encoded-value\> rule:
```abnf
 encoded-value = encoded_format encoded_data
                 ; elements order implied by the above rule MAY be ignored
```
;where \<encoded_format\> has type DevString and \<encoded_data\> has type DevVarCharArray. 

DevVarLongStringArray and DevVarDoubleStringArray values are structures to carry sequences of numbers 
(integer or double precision floating point, respectively) along with sequences of text/string data.

* Names DevVarLongStringArray and DevVarDoubleStringArray SHALL follow the following rules:
```abnf
 DevVarLongStringArray = "DevVarLongStringArray"
 DevVarDoubleStringArray = "DevVarDoubleStringArray"
```

* Values of DevVarLongStringArray and DevVarDoubleStringArray SHALL follow rules \<long-string-value\> 
and \<double-string-value\>, respectively:
```abnf
 long-string-value = lvalue svalue 
 double-string-value = dvalue svalue
                       ; elements order implied by the above rules MAY be ignored
```
;where \<lvalue\> has type DevVarLongArray, \<dvalue\> has type DevVarDoubleArray and \<svalue\> has type 
DevVarStringArray.

#### State

DavState type standardize a way of describing state of Device. Its values are from predefined set of 14 names.   

* DevState name SHALL be according to the following rule:
```abnf
 DevState = "DevState
```

* Value of DevState type SHALL follow the rule \<dev-state-value\>:
```abnf
 dev-state-value = "ALARM" / "INSERT" / "STANDBY" / "CLOSE" / "MOVING" / "UNKNOWN" / "DISABLE" / "OFF"
 dev-state-value =/ "EXTRACT" / "ON" / "FAULT" / "OPEN" / "INIT" / "RUNNING"
```

#### Enumeration

DevEnum data type allows to assign string Labels to DevShort values. It is valid only for Attributes. 

* DevEnum name SHALL follow the following rule:
```abnf
 DevEnum = "DevEnum"
```

* Label values of DevEnum SHALL follow the rule \<devenum-label\>:
```abnf
 devenum-label = 1*VCHAR
```  

* DevEnum labels SHALL be unique in the context of an Attribute.
* Each of DevEnum labels SHALL correspond to one and unique DevShort value. 
* Related DevShort values SHALL be consecutive and SHALL start with 0. 
* Values of DevEnum DataType SHALL be transported as values of DevShort.

* For any of Attribute of DevEnum data type, list of Labels SHALL be available as `enum_labels` Attribute property.

* Tango Controls SHALL allow a Tango Client to retrieve labels associated with the DevShort value.

#### Pipe

DevPipeBlob is a Data Type to transfer data related to Pipes (see 7/Pipe).

* DevPipeBlob SHALL have the following name:
```abnf
 DevPipeBlob = "DevPipeBlob"
```

* A value of DevPipeBlob type SHALL follow the \<pipe-blob-value\> rule:

```abnf
 pipe-blob-value = name blob-data
                   ; elements order implied by the above rule MAY be ignored

 name = 1*VCHAR 

 blob-data = *blob-data-element
 
 blob-data-element = name value inner-blob inner-blob-name 
                     ; elements order implied by the above rule MAY be ignored    

 inner-blob = blob-data  
```

