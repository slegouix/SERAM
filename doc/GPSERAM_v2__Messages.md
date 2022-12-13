GP SERAM Messages
=================

Handshake Command Message
-------------------------

### Message description

The Handshake Command Message is the Message used by the Local Agent
during the Handshake step as describes in ==section 2.3.2==. This is the
first message exchanges between a Local Agent and a Remote Agent.

### Message Properties

The next table lists properties associated with this Message:

| **Name**                    | **Description**                                                 | **No.** | **MOC** |
|-----------------------------|-----------------------------------------------------------------|---------|---------|
| sessionID                   | Identifier of the current Remote Management Session             | 1       | M       |
| secureElements              | A list of Secure Element available on Local Agent side          | 1       | M       |
| supportedTransportProtocols | A list of Transport Protocol supported by the Local Agent       | 1       | O       |
| supportedDataFormats        | A list of Data Format supported by the Local Agent.             | 1       | O       |
| supportedVersions           | A list of versions of GP SERAM RAM supported by the Local Agent | 1       | O       |

The items of secureElements property may be one of the following values:

-   eSE

-   uicc

-   euicc

The supportedTransportProtocols property may be one the following
values:

-   HTTPS

-   DTLS

The supportedDataFormats property may be one of the following values:

-   application/json

-   application/asn1

The supportedVersions property may be one of the following values:

-   1.0.1

-   2.0

Handshake Response Message
--------------------------

### Message description

The Handshake Response Message is the Message used by the Remote Agent
during the Handshake step as describes in section 2.3.2.

### Message Properties

The next table lists properties associated with this Message:

| **Name**                | **Description**                                      | **No.** | **MOC** |
|-------------------------|------------------------------------------------------|---------|---------|
| sessionID               | Identifier of the current Remote Management Session  | 1       | M       |
| chosenSecureElement     | The Secure Element selected by the Remote Agent      | 1       | C       |
| chosenTransportProtocol | The Transport Protocol selected by the Remote Agent  | 1       | C       |
| chosenDataProtocol      | The Data Protocol selected by the Remote Agent.      | 1       | C       |
| chosenVersion           | The Version of GP SERAM selected by the Remote Agent | 1       | C       |

Each value of the chosen properties shall correspond of one supported by
the Local Agent.

If the Remote Agent capabilities doesn’t match those of Local Agent,
none of the chosen properties shall be set.

### Message State Machine

The following table lists the possible exchanges before and after this
one:

| **Previous Message** | **Next Message**  |
|----------------------|-------------------|
| Handshake Command    | RAM Start Command |

RAM Start Command Message
-------------------------

### Message description

The RAM Start Command inform the Local Agent of the start of the Remote
Management Session by the Remote Agent.

### Message Properties

The next table lists properties associated with this Message:

| **Name**  | **Description**                                     | **No.** | **MOC** |
|-----------|-----------------------------------------------------|---------|---------|
| sessionID | Identifier of the current Remote Management Session | 1       | M       |

SE RAM Command Message
----------------------

### Message description

The SE RAM Command Message is used to send APDU to the Secure Element.

### Message Properties

The next table lists properties associated with this Message:

| **Name**    | **Description**                                                                                                                                                                  | **No.** | **MOC** |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|---------|
| sessionID   | Identifier of the current Remote Management Session                                                                                                                              | 1       | M       |
| stopOnError | Boolean value which determines whether the Local Agent shall continue sending the remaining APDU on Secure Element errors. If this property is not set, a true value is assumed. | 1       | O       |
| listOfCAPDU | An ordered list of APDU to send to the Secure Element. Each APDU are encoded as hexadecimal string.                                                                              | 1       | M       |

The Local Agent SHALL send each APDU command of the listOfCAPDU to the
Secure Element using the SE Access API. The Local Agent SHALL add each
APDU response to the SE RAM Response Message. If an APDU Response is not
available, the Local Agent SHALL use an empty string as APDU Response.
On any error to transmit an APDU command to the Secure Element, the
Local Agent SHALL discard all the remaining APDU commands and SHALL not
include any APDU response for the faulty transmission. Any warning or
error APDU response (i.e. 69xx or 68xx) are valid APDU response that
SHALL be inserted in the SE RAM Response. If the stopOnError flag of the
SE RAM Command is set to “true", the Local Agent SHALL not execute any
remaining APDU command after an error APDU response (i.e. 69xx).

SE RAM Response Message
-----------------------

### Message description

The SE RAM Response Message contains the APDU responses of the Secure
Element to a previous SE RAM Command Message.

### Message Properties

The next table lists properties associated with this Message:

| **Name**    | **Description**                                                                                      | **No.** | **MOC** |
|-------------|------------------------------------------------------------------------------------------------------|---------|---------|
| sessionID   | Identifier of the current Remote Management Session                                                  | 1       | M       |
| listOfRAPDU | An ordered list of APDU response of the Secure Element. Each APDU are encoded as hexadecimal string. | 1       | M       |

For each APDU send to the Secure Element, the Local Agent shall add the
associated APDU response to the listOfRAPDU property. If an APDU
response is not available (e.g. for a SELECT or on error), it shall be
filled with an empty string.

Notification Command Message
----------------------------

### Message description

The Notification Command Message is used to send processing notification
to the Device Application. The meaning of the notification and how it is
handled by the Device Application is implementation dependent

### Message Properties

The next table lists properties associated with this Message:

| **Name**              | **Description**                                          | **No.** | **MOC** |
|-----------------------|----------------------------------------------------------|---------|---------|
| sessionID             | Identifier of the current Remote Management Session      | 1       | M       |
| deviceAppNotification | An UTF-8 string to be notified to the Device Application | 1       | M       |

RAM Stop Command Message
------------------------

### Message description

A Remote Agent uses this Message to mark the end of its processing and
that no further Message will be sent.

Otherwise, the Local Agent shall notify the end of the Management
Session to the Device Application.

This Message marks the end of the processing by a Management Platform
and no further Message will be sent.

### Message Properties

The next table lists properties associated with this Message:

| **Name**  | **Description**                                                                                                           | **No.** | **MOC** |
|-----------|---------------------------------------------------------------------------------------------------------------------------|---------|---------|
| sessionID | Identifier of the current Remote Management Session                                                                       | 1       | M       |
| statusMsg | An UTF-8 string which details the final status of the processing. If not provided an empty string value shall be assumed. | 1       | O       |

The Local Agent shall be sent the statusMsg to the Device Application.

