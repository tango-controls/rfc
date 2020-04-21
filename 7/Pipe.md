---
domain: rfc.tango-controls.org 
shortname: 7/PIPE 
name: The pipe 
status: raw 
editor: Reynald Bourtembourg `<at esrf.fr - reynald.bourtembourg>`
contributors: Olga Merkulova `<at ingvord.ru - olga>`, Igor Khokhriakov `<at ingvord.ru - mail>`
---

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards
Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).


# Tango Pipe specification

This specification describes the Tango Pipe feature of a Device. 

## Goals

Tango Pipe is an Attribute [RFC-4] with custom data structure - the pipe name and pipe description.
But in contrary of Commands or Attributes, a pipe does not have a pre-defined data type: it may be of any basic Tango data type (or array of) and this may change every time a pipe is read or written.

## Use Case

1. To pass big pieces of data structure at a time
2. To define complex data structures to act as a single Attribute

## Specification

Device MAY define zero, one or more Pipes.

Pipe MUST have static metadata:

 * name
 * description
 * label
 * level
 * writeType
 * writable

```abnf
name = TangoName

description = DevString [link to RFC-9 Data Types]

label = DevString [link to RFC-9 Data Types]

level = "OPERATOR"/"USER"

writeType = "PIPE_READ"/"PIPE_WRITE"

writable = DevBoolean
```

Pipe name MUST follow Tango naming conventions.

Pipe with writeType="PIPE_READ" MUST be read only.

Pipe data structure MUST be:

 * Key(s) -> Value(s)

```abnf
Key = DevString [link to RFC-9 Data Types]

Value = Scalar or Array of Any Tango compliant type or Pipe [link to RFC-9 Data Types]
```

In Tango 9 "DevPipeBlob" is used to describe Value (see image below).

![](pipe_Tango_9.png)


> **V10 NOTE:** Attribute being a corner case of a Pipe can be considered as a derivative of a Pipe.

### Pipe events

Pipe MUST NOT be polled.

Pipe MAY emit an event via Server API call.

### Server/Client APIs

Server API MUST provide a set of methods for a Server Client code to define a Pipe.

Server API MUST provide a way to fill the Pipe with data.

Client API MAY provide a way to obtain Pipe schema.

Client API MUST provide methods for a client to fetch Pipe data from a Device.

Client API MUST provide methods to traverse Pipe structure.

