# JT/T808 Protocol for G10LS

## 1. Protocol Basics

### 1.1 Communication Method

The communication method used in this protocol should comply with the relevant provisions in JT/T 794. The communication protocol is TCP, with the platform acting as the server and the terminal as the client. Custom or extended content in this protocol document is marked in **red bold**.

### 1.2 Data Types

The data types used in the protocol messages are shown in Table 1:

**Table 1: Data Types**

| Data Type | Description and Requirements |
|---|---|
| `BYTE` | Unsigned single-byte integer (8 bits) |
| `WORD` | Unsigned double-byte integer (16 bits) |
| `DWORD` | Unsigned four-byte integer (32 bits) |
| `BYTE[n]` | n bytes |
| `BCD[n]` | 8421 code, n bytes |
| `STRING` | GBK encoding, terminated with a null character (0). If there is no data, a null terminator is still present. |

### 1.3 Transmission Rules

The protocol uses big-endian network byte order to transmit words and double words.

The conventions are as follows:
- **BYTE:** Transmitted as a byte stream.
- **WORD:** The high byte is transmitted first, followed by the low byte.
- **DWORD:** Transmitted in the order of high 24 bits, high 16 bits, high 8 bits, and finally low 8 bits.

### 1.4 Message Composition

#### 1.4.1 Message Structure

Each message consists of an identifier, a message header, a message body, and a checksum. The message structure is shown in Figure 1:

`Identifier | Message Header | Message Body | Checksum | Identifier`

**Figure 1: Message Structure**

#### 1.4.2 Identifier

The identifier is `0x7e`. If `0x7e` appears in the checksum, message header, or message body, it must be escaped. The escape rules are as follows:
- `0x7e` <--> `0x7d` followed by `0x02`
- `0x7d` <--> `0x7d` followed by `0x01`

The escaping process is as follows:
- **Sending a message:** Message encapsulation -> Calculate and fill the checksum -> Escape.
- **Receiving a message:** Unescape -> Verify the checksum -> Parse the message.

**Example:**
To send a data packet with the content `0x30 0x7e 0x08 0x7d 0x55`, the encapsulated packet would be: `0x7e 0x30 7d 0x02 0x08 0x7d 0x01 0x55 0x7e`.

### 1.4.3 Message Header

The content of the message header is detailed in Table 2:

**Table 2: Message Header Content**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Message ID | `WORD` | |
| 2 | Message Body Attributes | `WORD` | See Figure 2 for the format structure of the message body attributes. |
| 4 | Terminal Phone Number | `BCD[6]` | This field is the terminal device number on the device casing, a total of 11 digits. The device number is prepended with 0s for upload. For example, for 138081234567, the data uploaded is 0138081234567. |
| 10 | Message Sequence Number | `WORD` | Starts from 0 and increments with each message sent. |
| 12 | Message Packet Encapsulation | | If the relevant identification bit in the message body attributes indicates that the message is packetized, this item will have content; otherwise, it will not. |

The format structure of the message body attributes is shown in Figure 2:

`15 | 14 | 13 | 12 | 11 | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0`
`Reserved | Sub-package | Data Encryption Method | Message Body Length`

**Figure 2: Message Body Attribute Format Structure**

**Data Encryption Method:**
- bits 10-12 are the data encryption identification bits.
- When these three bits are all 0, it means the message body is not encrypted.
- When bit 10 is 1, it means the message body is encrypted with the RSA algorithm.
- Others are reserved.

**Sub-package:**
- When bit 13 of the message body attributes is 1, it indicates that the message body is a long message and will be sent in packets. The specific packet information is determined by the message packet encapsulation item.
- If bit 13 is 0, there is no message packet encapsulation item field in the message header.

The content of the message packet encapsulation item is shown in Table 3:

**Table 3: Message Packet Encapsulation Item Content**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Total Number of Message Packets | `WORD` | The total number of packets after the message is packetized. |
| 2 | Packet Sequence Number | `WORD` | Starts from 1. |

### 1.4.4 Checksum

The checksum is a single byte calculated by XORing each byte from the message header to the byte before the checksum.

## 2. Data Format

### 2.1 Terminal Universal Response [0001]

**Message ID:** 0x0001

The message body data format for the terminal universal response is shown in Table 4.

**Table 4: Terminal Universal Response Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding platform message. |
| 2 | Response ID | `WORD` | The ID of the corresponding platform message. |
| 4 | Result | `BYTE` | 0: Success/Confirmation; 1: Failure; 2: Message error; 3: Not supported. |

### 2.2 Platform Universal Response [8001]

**Message ID:** 0x8001

The message body data format for the platform universal response is shown in Table 5.

**Table 5: Platform Universal Response Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding terminal message. |
| 2 | Response ID | `WORD` | The ID of the corresponding terminal message. |
| 4 | Result | `BYTE` | 0: Success/Confirmation; 1: Failure; 2: Message error; 3: Not supported; 4: Alarm processing confirmation. |

### 2.3 Terminal Heartbeat [0002]

**Message ID:** 0x0002

The terminal heartbeat data message body is empty.

The platform responds with a universal response.

### 2.4 Terminal Registration [0100] 2011 Version

**Message ID:** 0x0100

The message body data format for terminal registration is shown in Table 6.

*Table 6-1: Terminal Registration Message Body Data Format (2011 Version)
*

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Province ID | `WORD` | Indicates the province where the terminal is installed. 0 is reserved and the platform will use the default value. The province ID uses the first two digits of the six-digit administrative division code specified in GB/T 2260. |
| 2 | City/County ID | `WORD` | Indicates the city and county where the terminal is installed. 0 is reserved and the platform will use the default value. The city/county ID uses the last four digits of the six-digit administrative division code specified in GB/T 2260. |
| 4 | Manufacturer ID | `BYTE[5]` | Five bytes, terminal manufacturer code. |
| 9 | Terminal Model | `BYTE[8]` | Eight bytes, this terminal model is defined by the manufacturer. If the number of digits is less than eight, it is padded with spaces. |
| 17 | Terminal ID | `BYTE[7]` | Seven bytes, composed of uppercase letters and numbers. This terminal ID is defined by the manufacturer. |
| 21 | License Plate Color | `BYTE` | The color of the license plate, as specified in JT/T 415-2006, 5.4.12. If the vehicle is not licensed, the value is 0. |
| 22 | License Plate | `STRING` | The motor vehicle license plate number issued by the public security traffic management department. |

### 2.4 Terminal Registration [0100] 2013 Version

**Message ID:** 0x0100

The message body data format for terminal registration is shown in Table 6.

**Table 6-2: Terminal Registration Message Body Data Format (2013 Version)**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Province ID | `WORD` | Indicates the province where the terminal is installed. 0 is reserved and the platform will use the default value. The province ID uses the first two digits of the six-digit administrative division code specified in GB/T 2260. |
| 2 | City/County ID | `WORD` | Indicates the city and county where the terminal is installed. 0 is reserved and the platform will use the default value. The city/county ID uses the last four digits of the six-digit administrative division code specified in GB/T 2260. |
| 4 | Manufacturer ID | `BYTE[5]` | Five bytes, terminal manufacturer code. |
| 9 | Terminal Model | `BYTE[20]` | 20 bytes, this terminal model is defined by the manufacturer. If the number of digits is insufficient, it is padded with "0x00" at the end. |
| 17 | Terminal ID | `BYTE[7]` | Seven bytes, composed of uppercase letters and numbers. This terminal ID is defined by the manufacturer. |
| 21 | License Plate Color | `BYTE` | The color of the license plate, as specified in JT/T 415-2006, 5.4.12. If the vehicle is not licensed, the value is 0. |
| 22 | License Plate | `STRING` | The motor vehicle license plate number issued by the public security traffic management department. |

### 2.5 Terminal Registration Response [8100]

**Message ID:** 0x8100

The message body data format for the terminal registration response is shown in Table 7.

**Table 7: Terminal Registration Response Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding terminal registration message. |
| 2 | Result | `BYTE` | 0: Success; 1: Vehicle already registered; 2: Vehicle not in the database; 3: Terminal already registered; 4: Terminal not in the database. |
| 3 | Authentication Code | `STRING` | This field is only present after a successful registration. |

The terminal will re-register every time it is reset. The platform needs to respond to registration messages at any time.

### 2.7 Terminal Authentication [0102]

**Message ID:** 0x0102

The message body data format for terminal authentication is shown in Table 8-1.

**Table 8-1: Terminal Authentication Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Authentication Code | `STRING` | The terminal reports the authentication code after reconnecting. |

**Table 8-2: Platform Response to Terminal Authentication Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding terminal message. |
| 2 | Response ID | `WORD` | 0x0102: Terminal Authentication Message ID. |
| 4 | Result | `BYTE` | 0: Success/Confirmation; 1: Failure. |

### 2.8 Set Terminal Parameters [8103]

**Message ID:** 0x8103

The message body data format for setting terminal parameters is shown in Table 9.

**Table 9: Terminal Parameter Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Total Number of Parameters | `BYTE` | |
| 1 | Parameter Item List | | See Table 10 for the parameter item format. |

**Table 10: Terminal Parameter Item Data Format**

| Field | Data Type | Description and Requirements |
|---|---|---|
| Parameter ID | `DWORD` | See Table 11 for the definition and description of each parameter ID. |
| Parameter Length | `BYTE` | |
| Parameter Value | | For multi-value parameters, multiple parameter items with the same ID are used in the message, such as the dispatch center phone number. |

**Table 11: Definition and Description of Each Terminal Parameter Setting**

| Parameter ID | Data Type | Description and Requirements |
|---|---|---|
| 0x0001 | `DWORD` | Terminal heartbeat sending interval, in seconds (s). |
| 0x0010 | `STRING` | Main server APN, wireless communication dial-up access point. |
| 0x0013 | `STRING` | Main server address, IP or domain name. |
| 0x0017 | `STRING` | Backup server address, IP or domain name. |
| 0x0018 | `DWORD` | Server TCP port. |
| 0x0020 | `DWORD` | Location reporting strategy, 0: timed reporting; 1: fixed-distance reporting; 2: timed and fixed-distance reporting. |
| 0x0027 | `DWORD` | Reporting interval during sleep, in seconds (s), >0. |
| 0x0029 | `DWORD` | Default time reporting interval, in seconds (s), >0. |
| 0x002C | `DWORD` | Default distance reporting interval, in meters (m), >0. |
| 0x0030 | `DWORD` | Inflection point supplementary reporting angle, <180°. |
| 0x0055 | `DWORD` | Maximum speed, in kilometers per hour (km/h). |
| 0x0056 | `DWORD` | Overspeed duration, in seconds (s). |
| 0x0080 | `DWORD` | Vehicle odometer reading, 1/10km. |
| 0x0081 | `DWORD` | Province ID of the vehicle's location. |
| 0x0082 | `DWORD` | City ID of the vehicle's location. |
| 0x0083 | `STRING` | Motor vehicle license plate number issued by the public security traffic management department. |
| 0x0084 | `BYTE` | License plate color, as specified in JT/T415-2006, 5.4.12. |

### 2.9 Query Terminal Parameters [8104]

**Message ID:** 0x8104

The query terminal parameters message body is empty.

### 2.10 Query Terminal Parameters Response [0104]

**Message ID:** 0x0104

The message body data format for the query terminal parameters response is shown in Table 12.

**Table 12: Query Terminal Parameters Response Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding terminal parameter query message. |
| 2 | Number of Response Parameters | `BYTE` | |
| 3 | Parameter Item List | | See Table 10 for the parameter item format and definition. |

### 2.11 Terminal Control [8105]

**Message ID:** 0x8105

The message body data format for terminal control is shown in Table 13.

**Table 13: Terminal Control Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Command Word | `BYTE` | See Table 14 for the description of the terminal control command word. |
| 1 | Command Parameter | `STRING` | The specific format of the command parameters is described later. Each field is separated by a semicolon (;). Each STRING field is first processed with GBK encoding and then assembled into a message. |

**Table 14: Terminal Control Command Word Description**

| Command Word | Command Parameter | Description and Requirements |
|---|---|---|
| 0x04 | None | Terminal reset (reboot). |
| 0x05 | None | Terminal restore to factory settings. |
| 0x17 | 2 bytes | Start continuous recording time, in minutes. |
| 0x18 | 1 byte | 0x00: Turn off control L; 0x01: Turn on control LS. |
| 0x64 | None | Cut off oil and electricity. |
| 0x65 | None | Restore oil and electricity. |
| 0x66 | None | External arming. |
| 0x67 | None | External disarming. |

### 2.12 Location Information Report [0200]

**Message ID:** 0x0200

The location information report message body consists of basic location information and a list of additional location information items. The message structure is shown in Figure 3.

`Basic Location Information | List of Additional Location Information Items`

**Figure 3: Location Report Message Structure**

The list of additional location information items is a combination of various additional location information items, and it can also be empty, which is determined by the length field in the message header.

The data format of the basic location information is shown in Table 16.

**Table 16: Basic Location Information Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Alarm Flag | `DWORD` | See Table 18 for the definition of the alarm flag bits. |
| 4 | Status | `DWORD` | See Table 17 for the definition of the status bits. |
| 8 | Latitude | `DWORD` | The latitude value in degrees multiplied by 10^6, accurate to one millionth of a degree. |
| 12 | Longitude | `DWORD` | The longitude value in degrees multiplied by 10^6, accurate to one millionth of a degree. |
| 16 | Altitude | `WORD` | Altitude, in meters (m). |
| 18 | Speed | `WORD` | 1/10km/h. |
| 20 | Direction | `WORD` | 0-359, with 0 being true north, clockwise. |
| 22 | Time | `BCD[6]` | YY-MM-DD-hh-mm-ss (GMT+8 time, all subsequent times in this standard use this time zone). |

**Table 17: Status Bit Definition**

| Bit | Status |
|---|---|
| 0 | 0: ACC off; 1: ACC on |
| 1 | 0: Not located; 1: Located |
| 2 | 0: North latitude; 1: South latitude |
| 3 | 0: East longitude; 1: West longitude |
| 4-5 | Reserved |
| 6 | 0: Disarmed; 1: Armed |
| 7-9 | Reserved |
| 10 | Oil circuit status: 0: Vehicle oil circuit normal; 1: Vehicle oil circuit disconnected |
| 11 | Power-off status: 0: Main power supply normal; 1: Main power circuit disconnected |
| 18 | GPS positioning flag when positioning is done by GPS |
| 19 | Beidou positioning flag when positioning is done by Beidou |

**Table 18: Alarm Flag Bit Definition**

| Bit | Definition | Handling Instructions |
|---|---|---|
| 0 | 1: Emergency alarm (SOS alarm) | Cleared after receiving a response. |
| 1 | 1: Overspeed alarm | The flag is maintained until the alarm condition is lifted. |
| 2 | 1: Fatigue driving | The flag is maintained until the alarm condition is lifted. |
| 3-6 | Reserved | |
| 7 | 1: Terminal main power undervoltage | The flag is maintained until the alarm condition is lifted. |
| 8 | 1: Main power off alarm (power-off alarm) | Alarm three times. |
| 9-14 | Reserved | |
| 15 | Battery low alarm (wireless device) | Cleared after receiving a response. |
| 16 | Vibration alarm | Cleared after receiving a response. |
| 17 | Removal alarm (light sensor alarm) | Cleared after receiving a response. |
| 18 | Reserved | |
| 19 | 1: Overtime parking | The flag is maintained until the alarm condition is lifted. |
| 20-27 | Reserved | |
| 28 | 1: Illegal vehicle displacement | Cleared after receiving a response. |
| 29-31 | Reserved | |

The format of the additional location information items is shown in Table 19.

**Table 19: Additional Location Information Item Format**

| Field | Data Type | Description and Requirements |
|---|---|---|
| Additional Information ID | `BYTE` | 1-255 |
| Additional Information Length | `BYTE` | |
| Additional Information | | See Table 20 for the definition of additional information. |

**Table 20: Additional Information Definition**

| Additional Information ID | Additional Information Length | Description and Requirements |
|---|---|---|
| 0x01 | 4 | Mileage, `DWORD`, 1/10km, locally accumulated mileage of the terminal. |
| 0x2B | 4 | The data reported for the second fuel consumption data is the Changrun fuel consumption protocol data. |
| 0x30 | 1 | Network signal strength CSQ value, 0-31. |
| 0x31 | 1 | Number of GPS satellites, the number of satellites with a signal value greater than 25dB. |
| 0x51 | 16 | 16 bytes, 2 bytes per temperature group, a total of 8 temperature channels. |
| 0x52 | 1 | Forward and reverse rotation (0: unknown; 1: forward rotation (empty vehicle); 2: reverse rotation (heavy vehicle); 3: stop rotation). |
| 0x53 | 1+n*8 | 2G base station data: The first byte is the number of base stations, followed by n base station data; Base station data: 0-1 MCC; 2 MNC; 3-4 LAC; 5-6 CELLID; 6 signal strength. |
| 0x54 | 1+n*7 | Wifi data: The first byte is the number of wifi, followed by n wifi data; WIFI data: 0-5 wifiMac; 6 signal strength. |
| 0x56 | 2 | Internal battery level. Byte 1, power level 0-10. Byte 2, power percentage 1-100, some devices support. Please confirm with the manufacturer whether the device supports the second byte percentage! |
| 0x5D | 1+n*10 | 4G base station data: The first byte is the number of base stations, followed by n base station data; Base station data: 0-1 MCC; 2 MNC; 3-4 LAC; 5-8 CELLID; 9 signal strength. |
| 0x61 | 2 | Main power supply voltage value, unit 0.01V. |
| 0xF1 | 20 | ICCID, the terminal reports once after each platform authentication. |
| 0xF3 | 1 | Arming/disarming status, 0x00 for disarming, 0x01 for arming. |
| 0xF4 | 2 | Extended alarm bit, 16 bits, corresponding to alarm 1 for alarm, 0 for no alarm. Bit0: Rapid acceleration alarm; Bit1: Rapid deceleration alarm; Bit2: Hard braking alarm; Bit3: Sharp turn alarm; Bit4: Collision alarm; Bit5: Rollover alarm; BIT6: Extended IO1 input, 0 for low, 1 for high; BIT7: Extended IO2 input, 0 for low, 1 for high; Bit8-Bit15: Reserved. |
| 0xF5 | 6 | 3-axis data report of gSensor. 0-1 for X-axis data; 2-3 for Y-axis data; 4-5 for Z-axis data. Note: The 3-axis data is a signed 16-bit string, transmitted with the high byte first. |
| 0xF6 | 4 | Wireless device working mode. First byte is the device working mode: 1: Default mode; 2: Timed mode; 3: SMS switch machine mode; 4: Sleep flight mode; 5: Timed flight mode. Second byte is the device positioning mode: 0: WIFI positioning; 1: WIFI+GPS positioning; 2: GPS positioning. Third byte is the recording configuration status: 0: No recording configured; 1: Voice-activated recording; 2: Continuous recording. Fourth byte is for extended status, a total of 8 bits: Bit0: 0: Charger not connected, 1: Charger connected; Bit1: 0: Charging, 1: Charging complete. |

### 2.13 Location Information Query [8201]

**Message ID:** 0x8201

The location information query message body is empty.

### 2.14 Location Information Query Response [0201]

**Message ID:** 0x0201

The message body data format for the location information query response is shown in Table 24.

**Table 24: Location Information Query Response Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Response Sequence Number | `WORD` | The sequence number of the corresponding location information query message. |
| 2 | Location Information Report | | See 2.12 for the location information report. |

### 2.16 Bulk Location Data Upload (Supplementary Data) [0704]

The message body data format for bulk location data upload is shown in Table 26.

**Table 26: Bulk Location Data Upload Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Number of Data Items | `WORD` | The number of location report data items included, >0. |
| 1 | Location Data Type | `BYTE` | 0: Normal location bulk report; 1: Blind spot supplementary report. |
| 2 | Location Report Data Item | | See Table 27 for the definition of the location report data item. |

**Table 27: Location Report Data Item Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Location Report Data Length | `WORD` | The length of the location data body, n. |
| 2 | Location Report Data Body | `BYTE[n]` | The format is the same as the location report, see 2.13 for the definition. |

### 2.17 Text Information Downlink [8300]

**Message ID:** 0x8300

The message body data format for text information downlink is shown in Table 28.

**Table 28: Text Information Downlink Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Flag | `BYTE` | Text information flag bit [Fixed to 0x02]. See Table 29 for meaning. |
| 1 | Text Information | `STRING` | Maximum length of 1024 bytes, GBK encoded. |

**Table 29: Text Information Flag Bit Meaning**

| Bit | Flag |
|---|---|
| 0 | 1: Urgent |
| 1 | Reserved |
| 2 | 1: Text downlink passthrough |
| 3 | 1: Terminal TTS reading |
| 4 | 1: Advertising screen display |
| 5-7 | Reserved |

### 2.18 Report Text Information [6006]

**Message ID:** 0x6006

The message body data format for reporting text information is shown in Table 30. (Platform sends 8300, terminal responds with 6006)

**Table 30: Report Text Information Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Flag | `BYTE` | Fixed to 0x00 |
| 1 | Text Information | `STRING` | Maximum length of 1024 bytes, GBK encoded. |

### 2.19 Recording Related

The recording format is currently AMR file format. Voice-activated and continuous recording are controlled by the 0x8105 command.

**Multimedia Data Upload**

**Message ID:** 0x0801

The message body data format for multimedia data upload is shown in the table below.

**Multimedia Data Upload Message Body Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Multimedia ID | `DWORD` | >0 |
| 4 | Multimedia Type | `BYTE` | 0: Image; 1: Audio; 2: Video |
| 5 | Multimedia Format | `BYTE` | 0: JPEG; 1: TIF; 2: MP3; 3: WAV; 4: WMV; others reserved |
| 6 | Event Item Code | `BYTE` | 0: Platform command; 1: Timed action; 2: Robbery alarm trigger; 3: Collision/rollover alarm trigger; others reserved |
| 7 | Channel ID | `BYTE` | |
| 8 | Multimedia Data Packet | | Only the first data packet contains the 8 bytes from "Multimedia ID" to "Channel ID". Subsequent packets are direct multimedia data. Each packet carries a maximum of 1000 bytes of multimedia data, and the last packet is based on the actual file. |

The platform uses a universal response to reply to each multimedia data packet.

**Multimedia Data Upload Result**

**Message ID:** 0x8800

The message body data format for the multimedia data upload response is shown in the table below.

**Multimedia Data Upload Result Data Format**

| Start Byte | Field | Data Type | Description and Requirements |
|---|---|---|---|
| 0 | Multimedia ID | `DWORD` | >0 |
| 4 | Total Number of Retransmission Packets | `BYTE` | |
| 5 | Retransmission Packet ID List | | No more than 125 items. If this field is not present, it means all data packets have been received. |

After processing all media packets, the platform needs to send this message to inform the terminal that the file reception is complete or that corresponding packets need to be retransmitted. If the terminal does not receive this message within 5 seconds, it will also automatically exit the current file upload process.

## 3. Customer 808 Platform Compatibility Configuration Scheme

### 3.1 Status Bit Compatibility Configuration

| Configuration Item | Control Parameter | Default Configuration | Configuration Description |
|---|---|---|---|
| Arming Flag Bit | `808_SFFLAG` | Bit6 | Optional range 0-31, 255 to disable. |
| GPS Flag Bit (compatible with 2013 version) | `808_GPSFLAG` | Bit18 | Optional range 18 or 255. 255 to disable. |
| Beidou Flag Bit (compatible with 2013 version) | `808_BDFLAG` | Bit19 | Optional range 19 or 255. 255 to disable. |

**Note:** If you need to change the default bit, the modified bit cannot conflict with the standard protocol.

### 3.2 Alarm Bit Compatibility Configuration

| Configuration Item | Control Parameter | Default Configuration | Configuration Description |
|---|---|---|---|
| Battery Low Voltage Alarm | `BAT_ALARM_808` | Bit15 | Optional range 0-31, 255 to disable. |
| Vibration Alarm Bit | `VIB_ALARM_808` | Bit16 | Optional range 0-31, 255 to disable. |
| Removal Alarm Bit | `LIGHT_ALARM_808` | Bit17 | Optional range 0-31, 255 to disable. |
| Power-off Alarm Mode | `POWER_ALARM_808` | 0 | Optional range 0-1. 0 for continuous reporting, 1 for reporting only 3 times. |

**Note:** If you need to change the default bit, the modified bit cannot conflict with the standard protocol.

### 3.3 Extended Field Compatibility Configuration

| Configuration Item | Control Parameter | Default Configuration | Configuration Description |
|---|---|---|---|
| External Voltage Reporting | `808_EXPOW` | 0 | Optional range 0-1. 0 to use 0x61 field, 1 to use 0x2B field. |
| Arming Status Extended Field | `808_EXSF` | 0 | 0 to report 0xF3 field, 255 to disable reporting this field. |
| 808 Report Base Station | `808_LBS` | 1 | Optional range 0-1. 0 to disable, 1 to enable. |
| 808 Report WIFI | `WIFI_ENABLE` | 1 | Optional range 0-1. 0 to disable, 1 to enable. |
| Select Protocol 2011, 2013 Version | `808_SEL` | 0 | Optional range 0-1. 0 for 2011 version, 1 for 2013 version. |
| ICCID Field Reporting | `NO_UP_ICCID` | 0 | Optional range 0-1. 0 to allow reporting 0xF1 extended field, 1 to disable. |
| Dual IP Automatic Switching | `808_FIXED_IP` | 0 | Optional range 0-1. 0 to switch master and slave IP when the master IP connection is abnormal, 1 to not switch. |
| Extended IO Input Function | `EXIO_FUN` | 0 | This parameter needs to be configured to use the extended IO input. 0: Disable extended IO (default), 1: Allow extended IO. |

### 3.4 Serial Port Passthrough Function

Customers can set the matching baud rate according to the actual connected serial device.

**Serial Port Level:** Supports TTL, 232, and 485 serial connections depending on the hardware solution.

**Downlink single packet data maximum:** 512 bytes. The 808 protocol serial port ID occupies 1 byte, so the actual maximum downlink data is 511 bytes.

**Uplink single packet data maximum:** 1024 bytes. Data exceeding this length will not be processed by the terminal.

**Serial Port Reception Reporting Mode:**
Can be configured for limited length reporting and timeout reporting.
- `SZCS#UART_EXLEN=0`: When the length is set to 0, timeout reporting is used.
- `SZCS#UART_EXLEN=1000`: When the length is set to be greater than 0, and the serial port receives data greater than the limit, all data in the buffer is reported.

**Reception Timeout:** After the device receives the first byte from the serial port, if no new data is received within 200ms, the data in the buffer will be forwarded to the server.

**Platform Protocol Description:** The platform protocol only supports the 808 protocol (compatible with the standard 808 protocol).

**Uplink command:** 0x0900, the first byte is fixed to 0x41 (meaning serial port 1).

**Downlink command:** 0x8900, the first byte is automatically filtered by the terminal, no restrictions.

