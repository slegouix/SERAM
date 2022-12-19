GP SERAM - HTTP REST Binding
============================

This file details the HTTP REST binding which uses HTTPS
as Transport protocol layer and JSON as Data Format protocol layer.

The Local Agent acts as a HTTP client and the Remote Agent as a HTTP
Server. Each GP SERAM Message may be transmitted in exactly one HTTP
message or may be aggregated with other message using a Message
Pipelining during the Remote Management Step as described below.

==A formal API specification of the HTTP REST Binding is provided in Annex
A.== This specification complies with the \[OAS\] and may be used to
generate code skeleton.

Management Session and HTTP messages
------------------------------------

HTTP messages are attached with a Management Session based on the
sessionId. The sessionId is explicitly inserted in each HTTP request and
implicitly determined for HTTP response based on the client-server
paradigm of the HTTP protocol.

Implementors MAY uses another HTTP mechanism to reinforce the HTTP
session (e.g. HTTP cookies). A Local Agent SHOULD support HTTP cookie as
defined in \[RFC6265\].

HTTP Polling
------------

To allow the Remote Agent to send its Command Messages to the Local
Agent, an HTTP polling is used as server-push mechanisms.

During HTTP polling, some void HTTP messages MAY be used. A void HTTP
message is an HTTP message which doesn’t carry any Command or Response
Message, and which role is to maintain the communication channel between
the Remote Agent and the Local Agent.

The Local Agent SHALL uses HTTP polling whenever awaiting a Command
Message. It SHALL NOT use HTTP Polling whenever it’s processing the
previous Command Message unless the Command Message doesn’t require a
Response Message.

The Remote Agent may fine-tune the HTTP polling by controlling the delay
between an HTTP response and the next HTTP request. If the Remote Agent
doesn’t specify any delay, the Local Agent SHALL make a pause of 1
second before sending the next HTTP request. The Remote Agent MAY used a
value of 0 to deactivate the delay.

As describes in \[RFC6202\], The Remote Agent MAY use HTTP long polling
mechanism and wait a certain period of time before sending the void HTTP
response. Remote Agent SOULD NOT used period of time beyond 30 seconds
and Local Agent SHOULD at least support latency of 30 seconds between
HTTP request and HTTP response.

![HTTP polling](images/image11.png)

Message Pipelining
------------------

The Message Pipelining allows multiple Message Commands to be sent over
a single HTTP response. An HTTP server MAY use Message Pipelining. An
HTTP client SHALL support Message Pipelining. It SHALL handle each
Message Commands one by one and the corresponding Message Responses, if
any, SHALL be included in the next HTTP polling request.

If a Local Agent detect an error during the processing of a Message
Command, it SHALL discard all the remaining Message Commands and only
consider Message Response until the Command Message that raises the
error.

The Message Pipelining is only available during the Remote Management
Step.

HTTP REST Endpoints
-------------------

Tow endpoints are defined: one to make the Handshakes step and the
second to makes the Remote Management step.

| **Endpoint**      | **Description**        |
|-------------------|------------------------|
| /handshake        | Handshake step         |
| /remoteManagement | Remote Management Step |

Binding of the Handshake Step
-----------------------------

The Handshake Command SHALL be mapped to a HTTP POST request and the
associated Handshake Response to the associated HTTP Response.

Properties of the Handshake Command SHALL be bound according to next
table.

The body SHALL contains a JSON object with a "ramHandshakeCommand"
element.

| **Property Name**           | **HTTP or JSON** | **Detail**                                                                                                                          |
|-----------------------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| sessionId                   | HTTP             | A mandatory key/value query part of Handshake Endpoint. The key SHALL be "sessionId".                                               |
| secureElements              | JSON             | Mapped to a mandatory "secureElement" array element of "ramHandshake".                                                              |
| supportedTransportProtocols | JSON             | Mapped to a mandatory "secureTransportProtocols" array element of "ramHandshake".<br>If not provided, ["HTTPS"] is assumed.         |
| supportedDataFormats        | JSON             | Mapped to a non-empty "supportedDataFormats" array element of "ramHandshake".<br>If not provided, ["application/json"] is assumed.  |
| supportedVersions           | JSON             | Mapped to non-empty "supportedVersions" array element of ramHandshake.<br>If not provided, ["2.0.02"] is assumed.                   |

If the HTTP Server could not fulfill the client proposal, it SHALL
respond with a 406 Not Acceptable response code, and the HTTP Server
SHOULD respond with a 404 Not Found if the sessionId is unknown on
server side. Otherwise, the HTTP server SHALL respond with a 200 Ok
response with an HTTP body that SHALL contains a JSON object with a
"ramHandshakeResponse" element.

Properties of the Handshake Response SHALL be bound according to the next table.

| **Property Name**       | **level** | **Detail**                                                                                                            |
|-------------------------|-----------|-----------------------------------------------------------------------------------------------------------------------|
| sessionId               | HTTP      | Implicitly determined from the HTTP request                                                                           |
| chosenSecureElement     | JSON      | Mapped to a mandatory "chosenSecureElement" element of "ramHandshake" that contains the selected Secure Element.      |
| chosenTransportProtocol | JSON      | Mapped to a mandatory "chosenTransportProtocol" element of "ramHandshake" that contains the selected Transport layer. |
| chosenDataFormat        | JSON      | Mapped to a mandatory "chosenDataFormat" element of "ramHandshake" that contains the selected Data Format layer.      |
| chosenVersion           | JSON      | Mapped to a mandatory "chosenVersion" element of "ramHandshake" that contains the selected GP SERAM version.          |

Binding of the Remote Management
--------------------------------

### Binding of the HTTP request to the Remote Management Endpoint

During the Remote Management step, the HTTP client SHALL poll the
server. The poll request SHALL be an HTTP POST message that contains the
sessionId in a key/value format in the query part of the Remote
Management Endpoint, using "sessionId" as key.

Whenever some Message Responses shall be sent to the HTTP server, the
HTTP client SHALL add them to the polling request in a JSON body with a
"RemoteManagementMsg" element which is an JSON array. This item SHALL
NOT be empty. Items of the "RemoteManagementMsg" SHALL contains a
"ramResponseType" element with a value of "seRamResponse".

### SE RAM Response Binding

The SE RAM Response Message SHALL be mapped an HTTP POST message.
Properties of the SE RAM Response Message SHALL be bound according to
the next table.

| **Property Name** | **level** | **Detail**                                                                                                                                                                  |
|-------------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| sessionId         | HTTP      | A mandatory key/value query part of Remote Management Endpoint. The key SHALL be "sessionId".                                                                               |
| listOfRAPDU       | JSON      | A mandatory element of "RemoteManagementMsg" mapped to a "listofRAPDU" element that is a JSON array of APDU response. Each APDU response are encoded as hexadecimal string. |

### Binding of the HTTP Response for Remote Management Endpoint

On any error detected in the JSON properties of the HTTP request, the
Remote Agent SHALL immediately ends the Management Session by sending
the RAM Stop Command Message. The Remote Agent SHOULD respond with an
HTTP 404 Not Found if the sessionId is unknown on server side.

Otherwise, the Remote Agent SHALL respond with an HTTP 200 Ok response.
The HTTP response MAY include a JSON object as HTTP body with a
"remoteManagementOrder" element which is a JSON object. The
"remoteManagementOrder" object MAY contains a "delay" element or a
"ramCommand" element, or both. The "delay" element is an integer that
represent the number of second the Local Agent had to wait for the
polling request, as described in section 3.2.2. The "ramCommand" element
is a JSON array which items SHALL contains a "ramCommandType" element
with a value of "ramStartCommand" or "seRamCommand" or
"notificationCommand", or "ramStopCommand" according with the type of
Message Command.

If the "delay" element is not present in the HTTP response, the Local
Agent SHALL assume a one second default value for the HTTP polling.

The "ramCommand" SHALL contain at least one Command Message. If the
"ramCommand" contains more than one Command Message, the Local Agent
SHALL handles them as detailed in section 4.3.

### RAM Start Command binding

To send a RAM Start Command, the Remote Agent SHALL add an object in the
"ramCommand" object with a "ramStartCommand" value in the
"ramCommandType" element. The properties of the RAM Start Command SHALL
be bound according to the next table.

| **Property Name** | **HTTP or JSON** | **Detail**                                  |
|-------------------|------------------|---------------------------------------------|
| sessionId         | HTTP             | Implicitly determined from the HTTP request |

### SE RAM Command binding

To send a SE RAM Command, the Remote Agent SHALL add an object in the
"ramCommand" object with a "seRamCommand" value in the "ramCommandType"
element. The properties of the SE RAM Command SHALL be bound according
to the next table.

| **Property Name** | **HTTP or JSON** | **Detail**                                                                                                                                    |
|-------------------|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| sessionId         | HTTP             | Implicitly determined from the HTTP request                                                                                                   |
| stopOnError       | JSON             | Mapped to a "stopOnError" element of a "ramCommand" item that is a boolean value with a default "true" value.                                 |
| listOfCAPDU       | JSON             | Mapped to a "listOfCAPDU" element of a "ramCommand" item that is an array of UTF-8 string that represent APDU command encoded in hexadecimal. |

Any error to transmit a APDU command to the secure Element, or if the
Secure Element respond with a 69xx APDU response with a stopOnError flag
set to "true", the Local Agent SHALL discard all the remaining Command
Message in the Message Pipeline.

Transmission Errors
-------------------

The Local Agent and the Remote Agent relies on HTTP protocol to their
exchanges. Most of the transmission errors will be transparently resumed
by the TCP (Transmission Control Protocol) end to end stacks.

Nevertheless, some errors are unrecoverable at TCP level. This section
lists the rules Local Agent and Remote Agent shall follow to enforce the
Message delivery or error detection.

Errors may result in a failure on each level of the exchanges:

-   the Local Agent

-   the Remote Agent

-   the Device

-   the Remote Management Platform

-   the network

If the HTTPS connection is prematurely closed, the local Agent SHOULD
immediately try to re-establish the HTTPS connection with the Remote
Agent.

If the Local Agent could not establish an HTTPS connection or send an
HTTP request to the Remote Agent, it SHOULD retry during 5 minutes at
the most.

If the Local Agent doesn’t receive an HTTP response within a 40 second
delay from the Remote Agent, it SHOULD close the current HTTPS
connection and try to re-establish a new one.

If a Remote Agent could not send an HTTP response to a Local Agent
within a 30 second delay, it SHALL close the current HTTPS connection
and SHOULD awaiting the Local Agent reconnection.

A Remote Agent SHOULD consider having lost a Local Agent after a
5-minute delay.

==TBC==

