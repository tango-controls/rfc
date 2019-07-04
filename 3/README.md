---
domain: rfc.tango-controls.org

shortname: 3/Command

name: Command

status: draft

editor: [Sergi Blanchi-Torné](mailto:sblanch@cells.es)

contributors: [Vincent Hardion](mailto:vincent.hardion@maxiv.lu.se)

---

This document describes the specification of the Tango Command model, a guideline for the implementer of the Tango Control System.

See also: 2/Device 4/Attribute

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Command Specification

 The specification of Command aims to define a standard structure and type to use in any implementation of Tango.

### Goals

 A Command in a Tango Controls represents an action, a sequence or other procedure executed remotely by a Device. The meaning of commands is quite similar to the meaning of methods in object oriented design.

This specification aims to define a Command in order to be able to :
 
 * Execute action considering an argument

 
 * Execute an asynchronous action 

 
 * Expect a reply in any case within a certain timeout

 
 * Be represented by meta data

 

Additionally, Command may be used for:

 * Reading and writing device parameters, as an alternative to read and write Tango Attribute for legacy reason (although Tango Attribute is the prefer way).


### Use Cases

 The use of Command by the developer of Device allows to resolve different situation:

 * A command "Stop" can trigger the ending of a movement for a motor or an integration for a detector
 * A command "Send" can take any SCPI command as argument and wait for the reply
 * A command "Execute" can launch a macro which may take long time to be executed in the background. The reply is just an acknowledgement that the sequence will be executed. The current state of progress can be retrieve by the state of the Tango Device

## Specification

 A Tango Command is a strict definition of a function apply at the Device context:

 * The Command SHALL have a unique identifier, called the Command Name

 * The Command input or output are called Argument

 * The Command SHALL have one input Argument, called ARGIN

 * The Command SHALL have one output Argument, called ARGOUT
 
 An Argument represents the input and output of a Command and its specification can only applied to a Command. It can be specify as below:

 * Argument SHALL have an Argument Type which describe the type of the Argument Value

 * Argument SHALL have an Argument Type which describe the type of the Argument Value
 
 TODO: I don't know if the string representation of the argument type is only a client thing ( Jive, ). Even so maybe it can be define here.
 
 An Argument Value only exists during the phase of execution of a Command. 
 In this case a Command MAY have 2 Arguments Values, one for ARGIN and one for ARGOUT.
 
 A Argument type, value and  is specified as below:

 ``` ABNF
 argument = argument-type argument-repr argument-value
 argument-type = dev-void-type | dev-double-type | ...
 argument-repr = dev-void-repr | ... # Here sound strange
 argument-value = dev-void-value | dev-double-value | ...
 
 dev-void = dev-void-type dev-void-repr dev-void-value
 dev-void-type = 0x00
 dev-void-repr = DEVVOID | DevVoid
 dev-void-value = NULL
 
 dev-double = dev-double-type dev-double-repr dev-double-value
 dev-double-type = 0x02
 dev-double-repr = DEVDOUBLE | DevDouble
 dev-double-value = ...

 dev-boolean = dev-boolean-type dev-boolean-repr dev-boolean-value
 dev-boolean-type = 0x03
 dev-boolean-repr = DEVBOOLEAN | DevBoolean
 dev-boolean-value = ...

 dev-float = dev-float-type dev-float-repr dev-float-value
 dev-float-type = 0x02
 dev-float-repr = DEVFLOAT | DevFloat
 dev-float-value = ...

 dev-short = dev-short-type dev-short-repr dev-short-value
 dev-short-type = 0x02
 dev-short-repr = DEVSHORT | DevShort
 dev-short-value = ...

 dev-long = dev-long-type dev-long-repr dev-long-value
 dev-long-type = 0x02
 dev-long-repr = DEVLONG | DevLong
 dev-long-value = ...

 dev-long64 = dev-long64-type dev-long64-repr dev-long64-value
 dev-long64-type = 0x02
 dev-long64-repr = DEVLONG64 | DevLong64
 dev-long64-value = ...

 dev-string = dev-string-type dev-string-repr dev-string-value
 dev-string-type = 0x02
 dev-string-repr = DEVSTRING | DevString
 dev-string-value = ...

 dev-uchar = dev-uchar-type dev-uchar-repr dev-uchar-value
 dev-uchar-type = 0x02
 dev-uchar-repr = DEVUCHAR | DevUChar
 dev-uchar-value = ... (octet)

 dev-ushort = dev-ushort-type dev-ushort-repr dev-ushort-value
 dev-ushort-type = 0x02
 dev-ushort-repr = DEVUSHORT | DevUShort
 dev-ushort-value = ...

 dev-ulong = dev-ulong-type dev-ulong-repr dev-ulong-value
 dev-ulong-type = 0x02
 dev-ulong-repr = DEVULONG | DevULong
 dev-ulong-value = ...

 dev-ulong64 = dev-ulong64-type dev-ulong64-repr dev-ulong64-value
 dev-ulong64-type = 0x02
 dev-ulong64-repr = DEVULONG64 | DevULong64
 dev-ulong64-value = ...

 dev-boolean-array = dev-boolean-array-type dev-boolean-array-repr dev-boolean-array-value
 dev-boolean-array-type = 0x02
 dev-boolean-array-repr = DEVVARBOOLEANARRAY | DevVarBooleanArray
 dev-boolean-array-value = ... a dev-boolean*

 dev-double-array = dev-double-array-type dev-double-array-repr dev-double-array-value
 dev-double-array-type = 0x02
 dev-double-array-repr = DEVVARDOUBLEARRAY | DevVarDoubleArray
 dev-double-array-value = ... a dev-double*

 dev-float-array = dev-float-array-type dev-float-array-repr dev-float-array-value
 dev-float-array-type = 0x02
 dev-float-array-repr = DEVVARFLOATARRAY | DevVarFloatArray
 dev-float-array-value = ... a dev-float*

 dev-short-array = dev-short-array-type dev-short-array-repr dev-short-array-value
 dev-short-array-type = 0x02
 dev-short-array-repr = DEVVARSHORTARRAY | DevVarShortArray
 dev-short-array-value = ... a dev-short*

 dev-long-array = dev-long-array-type dev-long-array-repr dev-long-array-value
 dev-long-array-type = 0x02
 dev-long-array-repr = DEVVARLONGARRAY | DevVarLongArray
 dev-long-array-value = ... a dev-long*

 dev-long64-array = dev-long64-array-type dev-long64-array-repr dev-long64-array-value
 dev-long64-array-type = 0x02
 dev-long64-array-repr = DEVVARLONG64ARRAY | DevVarLong64Array
 dev-long64-array-value = ... a dev-long64*

 dev-char-array = dev-char-array-type dev-char-array-repr dev-char-array-value
 dev-char-array-type = 0x02
 dev-char-array-repr = DEVVARCHARARRAY | DevVarCharArray
 dev-char-array-value = ... a dev-char*

 dev-string-array = dev-string-array-type dev-string-array-repr dev-string-array-value
 dev-string-array-type = 0x02
 dev-string-array-repr = DEVVARSTRINGARRAY | DevVarStringArray
 dev-string-array-value = ... a dev-string*

 dev-ushort-array = dev-ushort-array-type dev-ushort-array-repr dev-ushort-array-value
 dev-ushort-array-type = 0x02
 dev-ushort-array-repr = DEVVARUSHORTARRAY | DevVarUShortArray
 dev-ushort-array-value = ... a dev-ushort*

 dev-ulong-array = dev-ulong-array-type dev-ulong-array-repr dev-ulong-array-value
 dev-ulong-array-type = 0x02
 dev-ulong-array-repr = devvarulongarray | devvarulongarray
 dev-ulong-array-value = ... a dev-ulong*

 dev-ulong64-array = dev-ulong64-array-type dev-ulong64-array-repr dev-ulong64-array-value
 dev-ulong64-array-type = 0x02
 dev-ulong64-array-repr = DEVVARULONG64ARRAY | DevVarULong64Array
 dev-ulong64-array-value = ... a dev-ulong64*

 dev-long-string-array = dev-long-string-array-type dev-long-string-array-repr dev-long-string-array-value
 dev-long-string-array-type = 0x02
 dev-long-string-array-repr = DEVVARLONGSTRINGARRAY | DevVarLongStringArray
 dev-long-string-array-lvalue = dev-long-array
 dev-long-string-array-rvalue = dev-string-array
 dev-long-string-array-value = dev-long-array dev-string-array

 dev-double-string-array = dev-double-string-array-type dev-double-string-array-repr dev-double-string-array-value
 dev-double-string-array-type = 0x02
 dev-double-string-array-repr = DEVVARDOUBLESTRINGARRAY | DevVarDoubleStringArray
 dev-double-string-array-dvalue = dev-double-array
 dev-double-string-array-rvalue = dev-string-array
 dev-double-string-array-value = dev-double-array dev-string-array

 dev-boolean-array = dev-boolean-array-type dev-boolean-array-repr dev-boolean-array-value
 dev-boolean-array-type = 0x02
 dev-boolean-array-repr = DEVVARBOOLEANARRAY | DevVarBooleanArray
 dev-boolean-array-value = ... a dev-boolean*

 dev-encoded = dev-encoded-type dev-encoded-repr dev-encoded-value
 dev-encoded-type = 0x02
 dev-encoded-repr = DEVENCODED | DevEncoded
 dev-encoded-encoded-format = dev-string
 dev-encoded-encoded-data = dev-char-array
 dev-encoded-value = dev-string dev-char-array

 dev-encoded-array = dev-encoded-array-type dev-encoded-array-repr dev-encoded-array-value
 dev-encoded-array-type = 0x02
 dev-encoded-array-repr = DEVVARENCODEDARRAY | DevVarEncodedArray
 dev-encoded-array-value = ... a dev-encoded*
 
 
 Additionally, the Command can be represented by meta data:

 * A Command MAY have a description text called Command Description
 
 
### Global Behaviour

 * The Command SHALL always return a result when executed

 * The execution of the Command SHALL be unique

 * The execution of the Command SHALL be unique

 * The execution of the Command SHALL apply ionly in the context of a Device

### Naming convention
 * The Command Name SHALL use the following convention:

 ``` ABNF
 command-name = 1*VCHAR
 ```



