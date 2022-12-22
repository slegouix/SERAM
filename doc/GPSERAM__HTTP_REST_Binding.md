GP SERAM - HTTP REST Binding
============================

This file details the *HTTP REST Binding* which uses HTTPS as [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocol layer and JSON as [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) protocol layer.

The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) acts as a HTTP client and the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) as a HTTP server. Each GP SERAM [Message](GPSERAM__TerminologyAndDefinitions.md#Message) may be transmitted in exactly one HTTP message.

The formal API specification of the *HTTP REST Binding* is provided in [OpenAPI format](/spec/gpseram.yaml). This specification file may be used to generate code skeleton.

The *HTTP REST Binding* SHALL comply with:
- [RFC2818 – HTTP Over TLS](https://www.rfc-editor.org/rfc/rfc2818)
- [RFC6265 – HTTP State Management Mechanism](https://www.rfc-editor.org/rfc/rfc6265)
- [RFC7230 – Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing](https://www.rfc-editor.org/rfc/rfc7230)
- [RFC7231 – Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://www.rfc-editor.org/rfc/rfc7231)
- [RFC7232 – Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests](https://www.rfc-editor.org/rfc/rfc7232)
- [RFC7234 – Hypertext Transfer Protocol (HTTP/1.1): Caching](https://www.rfc-editor.org/rfc/rfc7234)


Management Session and HTTP messages
------------------------------------

HTTP messages are attached with a [Managment Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession) based on the [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId). The [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) is explicitly inserted in each HTTP request and implicitly determined for HTTP response based on the client-server paradigm of the HTTP protocol.

Implementors MAY uses another HTTP mechanism to reinforce the HTTP session (e.g. HTTP cookies). A [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) SHOULD support HTTP cookies as defined in [RFC6265](https://www.rfc-editor.org/rfc/rfc6265).

HTTP Polling
------------

To allow the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) to send *Order* [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) to the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent), an HTTP polling is used as server-push mechanisms.

During HTTP polling, some void HTTP messages MAY be used. A void HTTP message is any *Order* or *Report* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) which doesn’t carry any [Command](GPSERAM__TerminologyAndDefinitions.md#Command) or [Response](GPSERAM__TerminologyAndDefinitions.md#Response), and which role is to maintain the communication channel between the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent). The first HTTP request to the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) is always a void HTTP message.

The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) SHALL uses HTTP polling whenever awaiting a *Order* [Message](GPSERAM__TerminologyAndDefinitions.md#Message). It SHALL NOT use HTTP polling whenever it’s processing the previous *Order* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) unless it doesn’t require any [Response](GPSERAM__TerminologyAndDefinitions.md#Response).

The [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) may fine-tune the HTTP polling by controlling the delay between an HTTP response and the next HTTP request. If the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) doesn’t specify any delay, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) SHALL make a pause of 1 second before sending the next HTTP request. The [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) MAY used a value of 0 to deactivate the delay.

As describes in [RFC6202](https://www.rfc-editor.org/rfc/rfc6202.html), the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) MAY use HTTP long polling mechanism and wait a certain period of time before sending the void HTTP response.
[Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) SHOULD NOT used period of time beyond 30 seconds and [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) SHOULD at least support latency of 30 seconds between HTTP request and HTTP response.

![HTTP polling](images/GP_SERAM__HTTP_Polling.png)

HTTP REST Endpoints
-------------------

Two endpoints are defined: one to make the [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) and the second to makes the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step).

| **Endpoint**      | **Description**        |
|-------------------|------------------------|
| /handshake        | Handshake Step         |
| /remoteManagement | Command Exchange Step |

Handshake Step
--------------

The *Handshake Command* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) SHALL be mapped to a HTTP POST request and the associated *Handshake Response* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) to the associated HTTP Response.

![HTTP messages](images/GP_SERAM__HTTP_Handshake.png)

JSON schema for the HTTP request and response during [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) SHALL fullfill the [GP SERAM OpenAPI specification](/spec/gpseram.yaml).

Command Exchange Step
---------------------

The *Order* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) SHALL be mapped to an HTTP response  and the associated *Report* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) to the next HTTP Request.

![HTTP messages](images/GP_SERAM__HTTP_Command_Exchange.png)

The JSON schema for the HTTP request and response during [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) SHALL fullfill the [GP SERAM OpenAPI specification](/spec/gpseram.yaml).


Transmission Errors
-------------------

The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) and the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) relies on HTTP protocol to their exchanges. Most of the transmission errors will be transparently resumed by the TCP (Transmission Control Protocol) end-to-end stack.

Nevertheless, some errors are unrecoverable at TCP level. This section lists the rules a [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) and [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) shall follow to enforce the [Message](GPSERAM__TerminologyAndDefinitions.md#Message) delivery or error detection.

Errors may result in a failure on each level of the exchanges:

-   the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent)

-   the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent)

-   the [Device](GPSERAM__TerminologyAndDefinitions.md#Device)

-   the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform)

-   the network

If the HTTPS connection is prematurely closed, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) SHOULD immediately try to re-establish the HTTPS connection with the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent).

If the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) could not establish an HTTPS connection or send an HTTP request to the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent), it SHOULD retry during 5 minutes at the most.

If the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) doesn’t receive an HTTP response within a 40 seconds delay from the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent), it SHOULD close the current HTTPS connection and try to re-establish a new one.

If a [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) could not send an HTTP response to a [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) within a 30 seconds delay, it SHALL close the current HTTPS connection and SHOULD await the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) reconnection.

A [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) SHOULD consider having lost a [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) after a 5-minute delay.

