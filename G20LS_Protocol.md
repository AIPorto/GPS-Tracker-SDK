# G20LS Protocol Document (2023 CANTRACK PROTOCOL)

## 1. Protocol Overview

This document aims to provide a clear and concise explanation of the G20LS protocol while ensuring the integrity of the information. The protocol primarily defines the communication commands and data packet formats between the device and the server.

### 1.1 Packet Types

Communication between the device and the server is mainly carried out through the following packet types:

| Type   | Description          |
| :----- | :------------------- |
| `HTBT` | Heartbeat Packet     |
| `V1`   | GPS Packet           |
| `V3`   | LBS Packet           |
| `V4`   | Confirmation Packet  |
| `V5`   | WiFi Packet          |

### 1.2 Command Structure

#### 1.2.1 Server-to-Device Command Format

When the server sends a command to the device, it uses the following structure:

```
*XX,YYYYYYYYYYYYYYY,CMD,HHMMSS,PARA1,PARA2,…#
```

- `*`: Command start character
- `XX`: Supplier name (ASCII)
- `,`: Separator
- `YYYYYYYYYYYYYYY`: 15-digit device IMEI number
- `CMD`: Command code
- `HHMMSS`: Time (Hour/Minute/Second)
- `PARA`: Command parameter
- `#`: Command end character

**Note:** The first letter of the command must be capitalized, and it must not contain any spaces.

#### 1.2.2 Device Response Data Structure

The general information structure for a device response to the server is as follows:

```
*XX,YYYYYYYYYYYYYYY,V1,HHMMSS,S,latitude,D,longitude,G,speed,direction,DDMMYY,equ_status #
```

**Example:**

```json
{
  "raw_data": "*HQ,865205030330012,V1,145452,A,2240.55181,N,11358.32389,E,0.00,0,100815,FFFFFBFF#",
  "parsed": {
    "supplier": "HQ",
    "imei": "865205030330012",
    "packet_type": "V1",
    "time": "145452",
    "gps_valid": "A",
    "latitude": "2240.55181",
    "lat_direction": "N",
    "longitude": "11358.32389",
    "lng_direction": "E",
    "speed_knots": "0.00",
    "direction": "0",
    "date": "100815",
    "status": "FFFFFBFF"
  }
}
```

## 2. Data Packet Details

### 2.1 V1 - GPS Packet

The GPS packet is used to report the device's geographical location information.

**Format:**

```
*XX,YYYYYYYYYYYYYYY,V1,HHMMSS,S,latitude,D,longitude,G,speed,direction,DDMMYY,equ_status#
```

**Field Definitions:**

| Field | Description |
| :--- | :--- |
| `*` | Command start character |
| `XX` | Supplier name (ASCII) |
| `,` | Separator |
| `YYYYYYYYYYYYYYY` | 15-digit device IMEI number |
| `V1` | Command code |
| `HHMMSS` | Time (Hour/Minute/Second) |
| `S` | Data validity bit (A: Valid, V: Invalid, B: BeiDou valid) |
| `latitude` | Latitude (Format: DDMM.MMMMM) |
| `D` | Latitude symbol (N: North, S: South) |
| `longitude` | Longitude (Format: DDDMM.MMMMM) |
| `G` | Longitude symbol (E: East, W: West) |
| `speed` | Speed (Range: 000.00 ~ 999.99 knots) |
| `direction` | Direction (True north is 0 degrees, clockwise) |
| `DDMMYY` | Date (Day/Month/Year) |
| `equ_status` | Device status (See `4.1 equ_status` for details) |
| `#` | Command end character |

**Note:** The speed unit is "knot", which needs to be converted to kilometers/hour (1 knot = 1.852 km/h).

### 2.2 V3 - LBS Packet

The LBS packet is used to report the device's base station information, providing a location reference when the GPS signal is weak.

**Format:**

```
*XX,YYYYYYYYYYYYYYY,V3,HHMMSS,Base_Info,Battery_Info,Failure_Info,Cont,DDMMYY,equ_status#
```

**Field Definitions:**

| Field | Description |
| :--- | :--- |
| `Base_Info` | Base station information (See `Base_Info` format below) |
| `Battery_Info` | Battery information (Hexadecimal value from 0x0000-0x0299) |
| `Failure_Info` | Reboot information (0-9) |
| `Cont` | Extended protocol end character (X) |

#### Base_Info Format

```
MCC,MNC,Base_Number,LAC1,Cell_ID1,RS1,dBm1,LAC2,Cell_ID2,RS2,dBm2,…
```

| Field | Description |
| :--- | :--- |
| `MCC` | Mobile Country Code (China: 460) |
| `MNC` | Mobile Network Code (China Mobile: 00, China Unicom: 01) |
| `Base_Number` | Number of Base Station IDs (00-99) |
| `LAC` | Location Area Code |
| `Cell_ID` | Cell ID |
| `RS` | Signal Strength |
| `dBm` | Received Signal Strength |

**Example:**

```json
{
  "raw_data": "*HQ,865205030330012,V3,000201,46000,07,009350,004022,132,-88,009350,004032,140,,009350,004031,139,,009350,004023,133,,009350,004033,127,,009350,004021,124,,010351,003942,118,,0256,0,X,010915,FFFFFBFF#",
  "parsed": {
    "supplier": "HQ",
    "imei": "865205030330012",
    "packet_type": "V3",
    "time": "000201",
    "base_info": {
      "mcc": "460",
      "mnc": "00",
      "base_count": "07",
      "base_stations": [
        {"lac": "009350", "cell_id": "004022", "rs": "132", "dbm": "-88"},
        {"lac": "009350", "cell_id": "004032", "rs": "140", "dbm": ""},
        {"lac": "009350", "cell_id": "004031", "rs": "139", "dbm": ""},
        {"lac": "009350", "cell_id": "004023", "rs": "133", "dbm": ""},
        {"lac": "009350", "cell_id": "004033", "rs": "127", "dbm": ""},
        {"lac": "009350", "cell_id": "004021", "rs": "124", "dbm": ""},
        {"lac": "010351", "cell_id": "003942", "rs": "118", "dbm": ""}
      ]
    },
    "battery_info": "0256",
    "battery_voltage": "3.27V",
    "battery_percentage": "90.8%",
    "failure_info": "0",
    "date": "010915",
    "status": "FFFFFBFF"
  }
}
```

### 2.3 V5 - WiFi Packet

For special device models that support WiFi positioning, the V5 packet is used to report WiFi information.

**Format:**

```
*XX,YYYYYYYYYYYYYYY,V5,HHMMSS,Wifi_Number,Wifi_Info,Battery_Info,DDMMYY,equ_status#
```

**Field Definitions:**

| Field | Description |
| :--- | :--- |
| `Wifi_Number` | Number of WiFi networks (0-8) |
| `Wifi_Info` | WiFi information (The first 12 digits are the MAC address, the last 2 digits are the signal strength) |

**Example:**

```json
{
  "raw_data": "*HQ,135790246811220,V5,8,11223344556677,11223344556677,11223344556677,11223344556677,11223344556677,11223344556677,11223344556677,11223344556677,0256,010815,FFFFFBFF#",
  "parsed": {
    "supplier": "HQ",
    "imei": "135790246811220",
    "packet_type": "V5",
    "wifi_count": "8",
    "wifi_info": [
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"},
      {"mac": "112233445566", "signal_strength": "77"}
    ],
    "battery_info": "0256",
    "date": "010815",
    "status": "FFFFFBFF"
  }
}
```

### 2.4 HTBT - Heartbeat Packet

The heartbeat packet is used to maintain a persistent connection between the device and the server and to report basic status.

**Format:**

```
*HQ,YYYYYYYYYYYYYYY,HTBT,Battery percent#
```

**Example:**

```json
{
  "device_upload": {
    "raw_data": "*HQ,135790246811220,HTBT,100#",
    "parsed": {
      "supplier": "HQ",
      "imei": "135790246811220",
      "packet_type": "HTBT",
      "battery_percent": "100"
    }
  },
  "server_response": {
    "raw_data": "*HQ,135790246811220,HTBT,100#"
  }
}
```

### 2.5 ICCID - SIM Card Information

Used to report the device's SIM card ICCID information.

**Example:**

```json
{
  "device_upload": {
    "raw_data": "*HQ,865770062606670,ICCID,89860121801609042193#",
    "parsed": {
      "supplier": "HQ",
      "imei": "865770062606670",
      "command": "ICCID",
      "iccid": "89860121801609042193"
    }
  },
  "device_response": {
    "raw_data": "*HQ.865770062606670,ICCID#"
  }
}
```

## 3. GPRS Commands

GPRS commands allow the server to send control instructions to the device over the network.

### 3.1 Command Format

**Response Format:**

```
*XX,YYYYYYYYYYYYYYY,V4,CMD,,command,reply#
```

- `command`: The content of the command
- `reply`: The content of the reply

### 3.2 Command Examples

The following are examples of commonly used GPRS commands. For more commands, please refer to the SMS command list.

#### Query Device Information (check)

```json
{
  "command": "check123456",
  "response": {
    "raw_data": "*HQ,135790246811220,V4,CMD,check,CHECK,IMEI:861358034573915,APN:internet,IP:www.gps228.com:8989,C:,GSM:31,GPS:1,ACC:1,TZ:+8:30,ACCON/ACCOFF:5/60,SP:80,1,LMT2:40,Pw:On,12062,KC:1,OIL:90%,BAT:80%#",
    "parsed": {
      "supplier": "HQ",
      "imei": "135790246811220",
      "packet_type": "V4",
      "command": "CMD",
      "original_cmd": "check",
      "device_info": {
        "imei": "861358034573915",
        "apn": "internet",
        "server": "www.gps228.com:8989",
        "gsm_signal": "31",
        "gps_status": "1",
        "acc_status": "1",
        "timezone": "+8:30",
        "acc_on_off": "5/60",
        "speed_limit": "80",
        "oil_level": "90%",
        "battery": "80%"
      }
    }
  }
}
```

#### Reboot Device (reset)

```json
{
  "command": "reset123456",
  "response": {
    "raw_data": "*HQ,135790246811220,V4,CMD,reset,RESET OK#",
    "parsed": {
      "supplier": "HQ",
      "imei": "135790246811220",
      "packet_type": "V4",
      "command": "CMD",
      "original_cmd": "reset",
      "result": "RESET OK"
    }
  }
}
```

#### Set Working Mode (MD)

```json
{
  "mode_commands": [
    {
      "mode": 1,
      "command": "MD123456 1 3 8 1",
      "description": "Default Mode"
    },
    {
      "mode": 2,
      "command": "MD123456 2 3 1:00 2:00",
      "description": "Timing Mode"
    },
    {
      "mode": 3,
      "command": "MD123456 3 3 120",
      "description": "Interval Mode"
    }
  ]
}
```

## 4. General Data Definition

### 4.1 equ_status - Device Status

The `equ_status` field consists of 4 bytes, representing hexadecimal values as ASCII characters, used to describe the vehicle status and alarm status. When parsing, it needs to be converted to binary format and read bit by bit.

**Status Bit Table (from low bit to high bit):**

| Bit | Byte 1 | Byte 2 | Byte 3 | Byte 4 |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) |
| 1 | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) |
| 2 | 0: Overspeed Alarm | 1: Removal Alarm | 1 (Reserved) | 1 (Reserved) |
| 3 | 1 (Reserved) | 1 (Reserved) | 0: Vibration Alarm | 1 (Reserved) |
| 4 | 1 (Reserved) | 1 (Reserved) | 0: Low Battery Alarm | 0: Geo-fence In Alarm |
| 5 | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) |
| 6 | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) |
| 7 | 1 (Reserved) | 1 (Reserved) | 1 (Reserved) | 0: Geo-fence Out Alarm |

**Note:** The status bits use negative logic, meaning `0` is the active state.

**Example Analysis: `FBFFFFFF`**

```json
{
  "status_hex": "FBFFFFFF",
  "status_bytes": ["FB", "FF", "FF", "FF"],
  "parsing": {
    "byte_2": {
      "hex": "FB",
      "binary": "11111011",
      "bit_analysis": {
        "bit_0": {"value": 1, "meaning": "Reserved"},
        "bit_1": {"value": 1, "meaning": "Reserved"},
        "bit_2": {"value": 0, "meaning": "Removal Alarm (Triggered)"},
        "bit_3": {"value": 1, "meaning": "Reserved"},
        "bit_4": {"value": 1, "meaning": "Reserved"},
        "bit_5": {"value": 1, "meaning": "Reserved"},
        "bit_6": {"value": 1, "meaning": "Reserved"},
        "bit_7": {"value": 1, "meaning": "Reserved"}
      }
    }
  },
  "active_alarms": ["Removal Alarm"]
}
```

**Parsing Explanation:**
1. Convert the hexadecimal `FB` to binary: `11111011`
2. Read the bit values from right to left (from low bit to high bit)
3. Bit 2 is `0`, which, according to the status bit table, indicates that the removal alarm is triggered
4. All other bits are `1`, indicating that the corresponding functions are not triggered or are reserved bits


