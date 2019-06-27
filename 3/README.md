---
domain: rfc.tango-controls.org
shortname: 3/Command
name: Command
status: draft
editor: Vincent Hardion (vincent.hardion@maxiv.lu.se)
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

 A Command in a Tango Controls represents an action, a sequence or other procedure executed remotely by a Device.

 Additionally, it aims to:
 * Execute action considering an argument

 * Expect a reply in any case

 * Be represented by meta data

 * Be used to read and write device parameters, as an alternative to read Tango Attribute 

### Use Cases

 The use of Command by the developer of Device allows to resolve different situation:

 * TODO see issue

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
 ```
 
 
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



