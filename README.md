# GPS Positioning Service Open API Documentation

## Overview
This document provides comprehensive technical specifications for third-party developers to integrate **GPS positioning services** into their applications (App, Web, and WeChat platforms). It details open API interfaces, request/response formats, core parameters, error handling, and critical business logic, enabling developers to implement device management, location tracking, alarm monitoring, and other GPS-related functionalities.


## Key Contents
1. **Preface & Fundamentals**  
   - Core concepts (e.g., `mds` token, unit ID, device ID, 13-bit UTC timestamp).  
   - API calling flow (login → obtain token → call functional interfaces).  
   - Default URLs (test: `apitest.18gps.net`; production: `api.18gps.net`) and supported map types (GOOGLECN, BAIDU, GAODE, QQ).  

2. **Mobile Terminal Interfaces**  
   - Core account operations: Login, password modification, device binding/unbinding.  
   - GPS-related functions: Get real-time alarms, historical tracks, latest device location (single/batch), device list.  
   - Device management: Modify device info, issue commands, query command results.  

3. **Supplementary Modules**  
   - Audio Recording: Get real-time/historical voice records, delete recordings.  
   - Circular Fence (CFEN): Create, modify, delete circular fences, and get fence lists.  
   - Authorization: Add/cancel device function authorization, query authorization lists.  
   - Web Terminal Interfaces: User management (add/delete/modify), batch device operations (add/transfer/delete), device album/video retrieval.  
   - OBD Box: Get fault codes, real-time vehicle condition data, and trip records.  

4. **Reference Tables**  
   - Alarm codes & descriptions (e.g., SOS: 0x01, overspeed: 0x06).  
   - Error code mapping (user-level, device-level, system-level errors).  


## Usage Notes
- **Token (`mds`)**: Required for 98% of interfaces, valid for 20 minutes (extended on each request; re-login if expired, indicated by `errorCode=403`).  
- **Request Methods**: GET/POST are interchangeable unless specified.  
- **Parameter Sensitivity**: Pay attention to case sensitivity of request parameters.  
- **Permissions**: Some interfaces (e.g., batch device operations) require specific user permissions; contact the business team for access if needed.  


## Intended Audience
Third-party developers integrating GPS positioning services into App, Web, or WeChat applications.


## Foreword

### 1. Preface【Please be sure to read the entire preface carefully before you begin】
Please be sure to read the entire preface carefully before you begin!
Please be sure to read the entire preface carefully before you begin!!
Please be sure to read the entire preface carefully before you begin!!!


#### Description

This document provides convenience for third-party developers to implement GPS positioning services in App, Web and WeChat fields. Please prepare your own domain name (the url to be called), and be sure to organize the request link according to the request parameters in the document, especially pay attention to the case of the request parameters.

#### Flow

Preparation: Open this document and display the directory first.

*   **wps software:** Click View - Click Navigation Pane.
*   **Office:** Press Ctrl+F, the following picture will appear, click the circled button.

**First:** Use your account and password to obtain mds through the login interface, whether you are logging in with an account (unit user) or a device number.

**Second:** Get the two values returned by the login interface, mds and id (this id is consistent with the login type, the unit id is returned for account login, and the device id is returned for device number login). These two values are very important.

**Third:** Officially enter the stage of calling function interfaces. With the above two values, you can get the unit list or device list and a series of other operations.

**Fourth:** But if you want to use the interface to get the latest location and historical track, you must first call the "Get Device List" interface, because it contains the corresponding relationship between macid (device number) and device id.

**Fifth:** The following figure shows the sequence, which is also the interpretation of the above 4 sentences:



mds: token, about 98% of interface calls must carry this parameter, obtained through the login interface. When the interface returns 403, it means the token has expired. The token is valid for 20 minutes. If data is continuously requested, the expiration time will be extended from the last request. If it really expires, please log in again!

Unit ID: The primary key of the user database (unique index in the database), a 32-bit Guid. (Unit users) can obtain this value when calling the [Login] interface. A series of interfaces related to the logged-in user involve this ID.

Device ID: The primary key of the device database (unique index in the database), a 32-bit Guid. (Obtained from the device list interface or [Login] interface (device number type)). A series of operations related to devices are involved.

Device number: an 8-15 digit string, one of the credentials for identifying terminal devices, widely used. This value can be obtained from the device list interface. Its position on the Web platform is shown in the figure below:




UTC Timestamp: The entire platform uniformly uses 13-digit UTC timestamp milliseconds, which is seconds * 1000. The figure below shows the timestamp obtained by javascript on the web page.

Request Method: The request method given at the beginning of the table is not fixed, and you can choose GET or POST as needed.

Default Test URL: apitest.18gps.net (It is recommended to use the official URL below)

Default Official URL: api.18gps.net

The entire platform involves MapType values, which have the following four types:

*   GOOGLECN
*   BAIDU
*   GAODE
*   QQ

They represent Google Maps, Baidu Maps, Gaode Maps, and Tencent Maps respectively.

## Alarm Number and Description

## Error Code Correspondence Table

| No. | Code | Meaning | No. | Code | Meaning | No. | Code | Meaning |
|---|---|---|---|---|---|---|---|---|
| 0 | 0x00 | Reserved | 21 | 0x15 | Overdue | 41 | 0x29 | Illegal alarm |
| 1 | 0x01 | SOS | 22 | 0x16 | Gateway (X) | 42 | 0x2A | Vehicle not in use |
| 2 | 0x02 | Anti-theft | 23 | 0x17 | Towing (X) | 43 | 0x2B | Vehicle not in use |
| 3 | 0x03 | Out of bounds | 24 | 0x18 | Over speed | 44 | 0x2C | Vehicle not in use |
| 4 | 0x04 | In/Out of bounds | 25 | 0x19 | 4s alarm | 45 | 0x2D | Headlight alarm |
| 5 | 0x05 | Power cut | 26 | 0x1A | OBD fault | 46 | 0x2E | Vehicle not in use |
| 6 | 0x06 | Over speed | 27 | 0x1B | Fuel alarm | 47 | 0x2F | Tire pressure alarm |
| 7 | 0x07 | Low speed | 28 | 0x1C | Safe driving reminder | 48 | 0x30 | Overload alarm |
| 8 | 0x08 | Low battery | 29 | 0x1D | High voltage | 49 | 0x31 | Overdue alarm |
| 9 | 0x09 | Vibration | 30 | 0x1E | OBD fault | 50 | 0x32 | Low voltage alarm |
| 10 | 0x0A | Movement | 31 | 0x1F | Anti-theft alarm | 51 | 0x33 | Low temperature alarm |
| 11 | 0x0B | Offline | 32 | 0x20 | Bluetooth alarm | 52 | 0x34 | Abnormal alarm |
| 12 | 0x0C | Entry/Exit area | 33 | 0x21 | GPS signal alarm | 53 | 0x35 | Low tire pressure |
| 13 | 0x0D | Out of area | 34 | 0x22 | GSM signal alarm | 54 | 0x36 | High tire pressure |
| 14 | 0x0E | Door open | 35 | 0x23 | Light sensor alarm | 55 | 0x37 | Upload alarm |
| 15 | 0x0F | Key off | 36 | 0x24 | Normal alarm | 56 | 0x38 | Two-point stop |
| 16 | 0x10 | Vehicle started | 37 | 0x25 | Offline alarm | 57 | 0x39 | Three-point stop |
| 17 | 0x11 | Collision | 38 | 0x26 | Disassembly alarm | 58 | 0x3A | Device separation |
| 18 | 0x12 | Towing | 39 | 0x27 | Parking alarm | 59 | 0x3B | Location alarm |
| 19 | 0x13 | GBO (X) | 40 | 0x28 | Interference alarm | 60 | 0x3C | Annual review expired |
| 61 | 0x3D | Insurance due | 81 | 0x51 | Vehicle setup | 101 | 0x65 | Battery overvoltage |
| 62 | 0x3E | Maintenance due | 82 | 0x52 | Vehicle fire | 102 | 0x66 | Charging fault |
| 63 | 0x3F | Mileage maintenance reminder | 83 | 0x53 | Overcurrent discharge | 103 | 0x67 | LED fault |
| 64 | 0x40 | Out of bounds | 84 | 0x54 | Overcurrent charge | 104 | 0x68 | GNSS module fault |
| 65 | 0x41 | Route deviation alarm | 85 | 0x55 | MOS over-temperature | 105 | 0x69 | GNSS antenna not connected |
| 66 | 0x42 | Route limit alarm | 86 | 0x56 | Low capacity | 106 | 0x6A | Fuel abnormal |
| 67 | 0x43 | Offline alarm | 87 | 0x57 | BMS fault alarm | 107 | 0x6B | Low oil alarm |
| 68 | 0x44 | Base station | 88 | 0x58 | Running abnormal | 108 | 0x6C | 3D collision alarm |
| 69 | 0x45 | Overtime alarm | 89 | 0x59 | Running normal | 109 | 0x6D | Emergency alarm |
| 70 | 0x46 | Vehicle towing alarm | 90 | 0x5A | Device insertion | 110 | 0x6E | Annual review expired |
| 71 | 0x47 | Vehicle vibration alarm | 91 | 0x5B | Device removal | 111 | 0x6F | Operational expired |
| 72 | 0x48 | Rapid acceleration alarm | 92 | 0x5C | Voice control alarm | 112 | 0x70 | Enclosure stop |
| 73 | 0x49 | Rapid deceleration alarm | 93 | 0x5D | Vehicle ignition | 113 | 0x71 | Enclosure outside stop |
| 74 | 0x4A | Route limit alarm | 94 | 0x5E | Battery over-temperature | 114 | 0x72 | Door open alarm |
| 75 | 0x4B | Sharp turn alarm | 95 | 0x5F | Collision alarm | 115 | 0x73 | Door close alarm |
| 76 | 0x4C | Rapid speed alarm | 96 | 0x60 | Anti-theft alarm | |
| 77 | 0x4D | Door open alarm | 97 | 0x61 | Screen off oil alarm | |
| 78 | 0x4E | Door close alarm | 98 | 0x62 | Charge MOS fault | |
| 79 | 0x4F | Emergency brake alarm | 99 | 0x63 | Discharge MOS fault | |
| 80 | 0x50 | Emergency brake alarm | 100 | 0x64 | Low battery | |

## Error Code Table [Old]

| Error Code | Meaning | Error Code | Meaning | Error Code | Meaning |
|---|---|---|---|---|---|
| 1 | Identity verification failed | 16 | Authorized until | 30 | Old password error |
| 2 | Cannot add device | 17 | Authorized account password error | 31 | QR code not used |
| 3 | Device information error, verification failed | 18 | No permission | 32 | This account is not managed by law enforcement or management |
| 4 | SIM card error | 19 | Authorized account exists | 33 | Timeout |
| 5 | Device exists, please... | 20 | Password must be at least 6 characters long and contain numbers and letters | 34 | Insufficient balance |
| 6 | Please activate the subordinate device, activate | 21 | Verification failed | 35 | QR code bound user |
| 7 | User does not exist | 22 | Verification code error | 36 | Send notification interval - one minute |
| 8 | Non-registered user cannot unbind [itraker] | 23 | Phone number format not supported | 37 | Test device exceeds 200 |
| 9 | Already bound by others | 24 | License plate number exists | 38 | QR code added on the same day |
| 10 | Device not correct | 25 | QR code does not exist | 39 | Not directly subordinate user |
| 11 | User does not exist | 26 | QR code exceeds 50000 | |
| 12 | Device does not exist | 27 | QR code not bound | |
| 13 | Superior unit does not exist | 28 | Account or password cannot be empty | |
| 14 | Password mismatch | 29 | Account or password cannot be empty | |
| 15 | IMEI number is registered or filled incorrectly, please check this IMEI or contact the service provider. | |

## Mobile Terminal

### 1. Login

Interface Name: url/GetDateServices.asmx/loginSystem
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/loginSystem?LoginName=fangbing&LoginPassword=123456&LoginType=ENTERPRISE&language=cn&ISMD5=0&timeZone=+08&apply=APP&loginUrl=http://vipapi.18gps.net/

| Parameter | Type | Required | Description |
|---|---|---|---|
| LoginName | String | Yes | Login Account |
| LoginPassword | String | Yes | Login Password |
| LoginType | String | Yes | Login Type [Unit User: ENTERPRISE / Device Login: USER] |
| language | String | Yes | Language Type [cn, Chinese; en, English] |
| timeZone | Int | Yes | Time Zone [Default 8] |
| apply | String | Yes | Application Type [Default APP] |
| ISMD5 | Int | No | MD5 Verification [Default 0 No verification] |
| loginUrl | String | No | http://appzzl.18gps.net/ |

Development Notes

1.  [Note] This is the first interface you encounter in this document. All subsequent interfaces have similar structures. Please provide timely feedback to the project initiator if anything is unclear.
2.  [Purpose] To obtain the token value, i.e., mds (mds can be understood as a symbol of the logged-in user's identity). If errorCode=403 is returned when using other interfaces, it means the mds has expired, and a new mds needs to be obtained by calling the login interface again.
3.  [Request] Required fields must be provided, otherwise an error will be reported. Default values are also acceptable; if 'No' appears, it can be filled or left blank, depending on the specific situation.
4.  [Response] The returned data is largely a standard JSON object string, but not absolutely. Please refer to the example, and the returned result shall prevail. The most common mistake is that people often mistakenly use the returned 'id' as the device 'id' to get the latest location. In fact, when the login type is "ENTERPRISE", the returned 'id' is the unit 'id'. If the login type is USER, it is the device 'id'. Please pay attention.

Request Result

Success:
```json
{
"id": "5aa9e28a-80e3-4e29-b2b1-02774724d0", // ID of the logged-in user (determined by login type, here it is unit ID)
"success": "true", // Whether login is successful
"mds": "Token user identity", // Token
"LoginType": "ENTERPRISE", // Login type: unit user login
"grade": 4, // Account level
"msg": "Login successful", // Error description
"errorCode": 200 // Error code
}
```
Failure:
```json
{
"id": "00000000-0000-0000-0000-000000000000",
"success": "false",
"mds": null,
"LoginType": "ENTERPRISE",
"grade": -2000,
"msg": "Username or password error",
"errorCode": 404
}
```

### 2. Account Registration (Deactivated, no longer in use, please use account 11 for new registration)

Interface Name: LXVehicleInfoRegister
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=LXVehicleInfoRegister&username=15019466955&password=123456&parentId=d99bf6240faf4cdd9771469db0db98c8

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| username | String | Yes | Registration Account/Mobile Number |
| password | String | Yes | Registration Password |
| parentId | Guid | Yes | Parent Unit ID (Direct relationship) |

Development Notes

The parentId parameter in the interface needs to specify a superior ID. All registered users belong to the user managed by this ID. This ID needs to be applied for from the business department.

Error Codes:

*   200 Registration successful
*   500 Registration failed
*   1001 Account already exists / Phone number already exists
*   1002 Parent unit does not exist, no parent ID filled in
*   1005 Account or password cannot be empty

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200", // Registration successful
"errorDescribe": "",
"data": []
}
```
Failure:
```json
{
"success": "false",
"errorCode": "1001", // Account already exists / Phone number already exists
"errorDescribe": "",
"data": []
}
```

### 3. Account Change Password

Interface Name: modifyEnterprisePwd
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=modifyEnterprisePwd&reg_pass=1234&reg_olbpass=123456&mds=c74c94d2c9da4ec49cdc3f2795b71dc1

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| reg_pass | String | Yes | New Password |
| reg_olbpass | String | Yes | Old Password |
| ISMD5 | Int | No | 0 No verification / 1 Verification |

Development Notes

1.  Whether MD5 verification is enabled or not, the new password reg_pass must be in plain text.
2.  MD5 verification must pass an uppercase value (the ciphertext after password encryption is required to be uppercase).

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "Modification successful"
}
```
Failure:
```json
{
"success": "false",
"errorCode": "500",
"errorDescribe": "Description"
}
```

### 4. Account (Bind/Add) Device

Interface Name: ActiveAndAddDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=ActiveAndAddDevice&login_id=3dfca26777e549338a72e03e1c354d65&mds=e4495806ce1a4d42a7f6eba2f9d36ef0&FullName=H25685&Macid=011610300002

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| login_id | Guid | Yes | Unit ID |
| Macid | String | Yes | Device Number (IMEI) |
| Platenumber | String | No | License Plate Number |
| FullName | String | No | Device Name (Nickname) |

Development Notes

To add a device to an account, the value of the parameter login_id is taken from the id field in the data returned during login.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{"user_id": "476f61b2c6c349f3a9fc3e727090cb7a"}]
}
```
Failure:
```json
{
"success": "false",
"errorCode": "9", // Already bound by others
"errorDescribe": "BoundedByOther",
"data": []
}
```

### 5. Account (Unbind/Clear) Device

Interface Name: unbundlingDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=unbundlingDevice&mds=e4495806ce1a4d42a7f6eba2f9d36ef0&macid=011610300002

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| login_id | Guid | Yes | Unit ID |
| macid | String | Yes | Device Number (IMEI) |

Development Notes

The function is opposite to the binding interface. Unbinding does not mean the device is completely deleted, but rather it returns to the account where it was first added, and can be retrieved through the binding interface.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

### 6. Get Real-time Alarms

Interface Name: queryLocalAlarmInfoUtc
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?timestamp=1508228270718&method=queryLocalAlarmInfoUtc&callback=timeWarnOBJ.loadDataCallBack&school_id=754a5a28-8f24-47ff-908f-c363663db852&mds=72311ebde9e94e1292152436303362ff&max_time=1508103827687&type=custom

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| school_id | Guid | Yes | Unit ID/Device ID |
| type | String | Yes | Fixed value "custom" |
| max_time | Long | Yes | Last alarm time (Utc timestamp milliseconds, controls the acquisition range) |
| timestamp | Long | No | Random number (used to refresh cache) |
| callback | String | No | Callback method name |

Development Notes

school_id can be one of the two login types.

Unit login can get a maximum of 150 alarms, and device login can get 60 alarms. The data comes from the cache.

If you want to get historical alarm records, please use 20. Get Alarm Details.

How to achieve real-time acquisition?

1.  The key is the max_time field.
2.  The first time you access the interface, assign a default value to max_time, such as 12:00 noon yesterday, then you will get alarm data from max_time (12:00 noon yesterday) to now.
3.  If data is returned (when total is not equal to 0), it means there is an alarm during this period. When organizing the data, compare max_time and send_time, assign the maximum value to max_time, so that max_time is always the time of the last alarm.
4.  Set a timer to poll the interface every 10 seconds. As long as the returned data is not empty, the value of max_time will change. If it is empty, it will not change, thus achieving real-time.
5.  If you don't need to use jsonp for callback, remove callback, and the returned will be a json object.

Request Result

Success:
```json
timeWarnOBJ.loadDataCallBack(
{
"total": 1,
"rows": [
{
"id": "1c20ee64-8eff-43aa-b77c-2a00c57cf3ce", // Alarm number (database primary key)
"macid": "864306200010433", // Device number
"course": 0, // Direction
"gps_status": "Expired today", // Description
"gps_time": 1508203827687, // Time when alarm data was received
"jingdu": 113.853685, // Longitude
"weidu": 22.5890067, // Latitude
"send_time": 1508203827687, // Alarm time
"speed": "0", // Speed
"status": 0, // Whether processed, 0 means unprocessed
"type_id": 61, // Alarm type
"classifyDescribe": 0,
(Content truncated due to size limit. Use page ranges or line ranges to read remaining content)
```



## 7. Get Latest Position (Single Device)

Interface Name: queryLocalAlarmInfoUtc
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetLastPosition&mds=e4495806ce1a4d42a7f6eba2f9d36ef0&macid=011610300002&mapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number (IMEI) |
| mapType | String | Yes | Map Type |

Development Notes

This interface is used to get the latest position of a single device. The mapType parameter specifies the map service to use (e.g., BAIDU, GOOGLECN).

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"macid": "011610300002",
"lat": 22.5890067,
"lon": 113.853685,
"gps_time": 1508203827687,
"speed": "0",
"course": 0,
"status": 0,
"address": "Shenzhen, Guangdong, China",
"mapType": "BAIDU"
}
]
}
```

## 8. Get Historical Track

Interface Name: GetHistoryTrack
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetHistoryTrack&mds=e4495806ce1a4d42a7f6eba2f9d36ef0&macid=011610300002&beginTime=1508103827687&endTime=1508203827687&mapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number (IMEI) |
| beginTime | Long | Yes | Start time (Utc timestamp milliseconds) |
| endTime | Long | Yes | End time (Utc timestamp milliseconds) |
| mapType | String | Yes | Map Type |

Development Notes

This interface is used to get the historical track of a single device within a specified time range.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"macid": "011610300002",
"lat": 22.5890067,
"lon": 113.853685,
"gps_time": 1508203827687,
"speed": "0",
"course": 0,
"status": 0,
"address": "Shenzhen, Guangdong, China",
"mapType": "BAIDU"
},
{
"macid": "011610300002",
"lat": 22.5891000,
"lon": 113.8537000,
"gps_time": 1508203837687,
"speed": "10",
"course": 90,
"status": 0,
"address": "Shenzhen, Guangdong, China",
"mapType": "BAIDU"
}
]
}
```

## 9. Get Device List

Interface Name: GetDeviceList
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetDeviceList&mds=e4495806ce1a4d42a7f6eba2f9d36ef0&mapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| mapType | String | Yes | Map Type |

Development Notes

This interface is used to get a list of all devices associated with the logged-in user. The returned data includes device details such as macid, current position, and status.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"macid": "011610300002",
"fullName": "Device 1",
"lat": 22.5890067,
"lon": 113.853685,
"gps_time": 1508203827687,
"speed": "0",
"course": 0,
"status": 0,
"address": "Shenzhen, Guangdong, China",
"mapType": "BAIDU"
},
{
"macid": "011610300003",
"fullName": "Device 2",
"lat": 22.5900000,
"lon": 113.8540000,
"gps_time": 1508203837687,
"speed": "10",
"course": 90,
"status": 0,
"address": "Shenzhen, Guangdong, China",
"mapType": "BAIDU"
}
]
}
```

## 10. Modify Device Information

Interface Name: SetGpsUserFullNameAndPlateNumber
Request Method: HTTP GET
Request Example: url/DataService.ashx/GetDate?method=SetGpsUserFullNameAndPlateNumber&macid=DeviceNumber&fullName=DeviceName&plateNumber=LicensePlateNumber&mds=(obtained from login interface)

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number (IMEI) |
| fullName | String | Yes | Device Name |
| plateNumber | String | Yes | License Plate Number |
| LinkName | String | No | Contact Person |
| LinkTel | String | No | Contact Number |
| sim | String | No | Device SIM Card Number |

Development Notes

Note that the last three are not required fields. You can fill them in for modification if needed.

Request Result

Success:
```json
{
"data": [],
"success": "true",
"errorCode": "200",
"errorDescribe": "Modification successful"
}
```

## 11. Account Registration (with device)

Interface Name: RegisterUser
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=RegisterUser&macid=xy1610100063&loginName=17150310018&password=123456&repassword=123456

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number (IMEI) |
| loginName | String | Yes | Username |
| password | String | Yes | Password |
| repassword | String | Yes | Confirm Password |

Development Notes

This interface can be used for registration in App or WeChat.

Request Result

General interface:

## 12. Send Command

Interface Name: SendCommands
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=SendCommands&macid=00005581D8F3&cmd=GETVERSION&param=&pwd=&sendTime=&mds=6efb1fda32bb4416a7a29e54a1b045d1

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number (IMEI) |
| mds | String | Yes | Token |
| cmd | String | Yes | Command Name |
| param | String | No | Parameters to send, separated by English commas |
| pwd | String | No | Device Password |
| sendTime | DateTime | No | Set sending time |

Development Notes

After the command is issued successfully, "CmdNo" command receipt number is returned.

```javascript
var backMess = { 
    flowID: 'Serial Number', 
    SEND_SUCCESS: 'Operation successful', 
    SEND_FAIL: 'Operation failed', 
    PWD_ERROR: 'Password error', 
    NOT_CUSTOMER: 'Customer does not exist', 
    USER_LEAVE: 'Current device is offline, scheduled to execute after going online.', 
    'Not Online': 'Current device is offline, scheduled to execute after going online.', 
    'PERMISSIONS': 'Experience account, cannot send commands', 
    Nonsupport: 'Command not supported, please contact for support', 
    SEND_OK: 'Command sent successfully', 
    Fail: 'Command failed to send', 
    DeviceNot: 'Device does not exist', 
    Cmd_ExceedLength: 'Warning: Queue is full, please go to [Command - Send Record] page to view!' 
};
```

*   Arming cmd: SAFEON (general case, special commands may not be this value)
*   Disarming cmd: SAFEOFF (general case, special commands may not be this value)
*   Transparent transmission cmd: PASSTHROUGH (parameter "0,5554" means text transmission 5554; "1,5554" means 16-hexadecimal transmission 0x55 0x54)

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"CmdNo": "e2b6b72c-27a3-4b86-9da0-477aa43a3518", // Receipt number
"ReturnMsg": "USER_LEAVE"
}]
}
```

## 13. Command Result Query

Interface Name: GetCommandResults
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetCommandResults&macid=00005581D8F3&cmdNo=e2b6b72c-27a3-4b86-9da0-477aa43a3518&mds=6efb1fda32bb4416a7a29e54a1b045d1

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number (IMEI) |
| mds | String | Yes | Token |
| cmdNo | String | Yes | Receipt Number |

Development Notes

None.

Request Result

Success:
```json
------
```

## 13.1. Query All Command Results

Interface Name: GetCommandResults
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetCommandResults&macid=00005581D8F3&cmdNo=e2b6b72c-27a3-4b86-9da0-477aa43a3518&mds=6efb1fda32bb4416a7a29e54a1b045d1

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number (IMEI) |
| mds | String | Yes | Token |
| cmdNo | String | Yes | Receipt Number |

Development Notes

None.

Request Result

Success:
```json
------
```

## 14. Request Latitude and Longitude

Interface Name: ---
Request Method: HTTP GET
Request Example: http://poi.18gps.net/POI?lat=22.36&lon=114.256&map=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| lat | Double | Yes | Latitude |
| lon | Double | Yes | Longitude |
| map | String | Yes | Map Type |

Development Notes

Map types refer to the preface.

Request Result

Success:
```json
26B2, 7th Lane, Ho Chung Village, Sai Kung District, Hong Kong SAR. 197 meters northwest of Liu Ren Yi Qi Shan Tan
```

## 15. Batch Get Latest Position

Interface Name: getDeviceListByCustomId
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getDeviceListByCustomId&mds=a1324221c41d44a08f93445b1bec63dd&id=e9c939f1-9c06-4312-b295-47f0162b4176&mapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |
| mapType | String | Yes | Map Type |

Development Notes

The returned data remarks are consistent with getting the latest position (single device).

Request Result

Success:
```json
{
"success":"true",
"errorCode":"200",
"errorDescribe":"",
"data":[
{
"key":{
"sys_time":0,
"user_name":1,
"jingdu":2,
"weidu":3,
"ljingdu":4,
"lweidu":5,
"datetime":6,
"heart_time":7,
"su":8,
"status":9,
"hangxiang":10,
"sim_id":11,
"user_id":12,
"sale_type":13,
"iconType":14,
"server_time":15,
"product_type":16,
"expire_date":17,
"group_id":18,
"statenumber":19,
"electric":20,
"describe":21,
"sim":22,
"precision":23,
"isFollow":24
},
"records":[
[1444202949000,"Tianyi Card",114.046648,22.652224,114.046648,22.652224,1444202951657,1444202951657,"5","00000100",319.0,"383715011929452","1fe02b6045a4477aae90e66a7c22(Content truncated due to size limit. Use page ranges or line ranges to read remaining content)"
```

## 16. Get Subordinate Unit Device List

Interface Name: getDeviceListByCustomId
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetDeviceListByCustomId&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |
| mapType | String | Yes | Map Type |

Development Notes

Get the device list of subordinate units.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // See latest single position
"sys_time": 0,
"user_name": 1,
"jingdu": 2,
"weidu": 3,
"ljingdu": 4,
"lweidu": 5,
"datetime": 6,
"heart_time": 7,
"su": 8,
"status": 9,
"hangxiang": 10,
"sim_id": 11,
"user_id": 12,
"sale_type": 13,
"iconType": 14,
"server_time": 15,
"product_type": 16,
"expire_date": 17,
"group_id": 18,
"statenumber": 19,
"electric": 20,
"describe": 21,
"sim": 22,
"precision": 23,
"isFollow": 24
},
"records": [
[1482596907000, "601603080377", 114.231935, 22.667007,
114.231935, 22.667007, 1482591004402, 1482596908894, "0", "00000100", 134.0, "601603080377", "2f45ed3a309744c5b9da11cc5bd3d5dd", 0, "22", 1551926835558, "DXMKT", 2141959763770, "1830a1e7cf8a4790b10e3bab2e7c2406", "813312.57,0,0,0,0,0,0,0,0,0,0,0,0,0,0", "1234458373", 10, "0"],
[0, "DXMKT7908511", 0.0, 0.0, 0.0, 0.0, 0, 0, "-9", "00000000", 0.0, "867273027908511", "559d5b39f46c4790b729d2a86f5bef31", 0, "01", 1551926835558, "DXMKT", 1542240000000, "0", "0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0", 10, "0"]
],
"groups": [
"followCount": 0, // Number of followers
"deviceCount": 3 // Number of devices
]
}
]
}
```

## 17. Bind Device Interface [Reserved]

Interface Name: MoveDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=MoveDevice&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d&macid=027028656666

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID (Self-written) |
| macid | String | Yes | Device Number |

Development Notes

If you have a registered account, you need to add another device to your account.

Request Result

Success:
```json
------
```

## 18. Set Alarm Filter [Common for Device Registration and Account Login]

Interface Name: setFilterAlarmInfo
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=setFilterAlarmInfo&custid=754a5a28-8f24-47ff-908f-c363663db702&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=0,3,7

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID (Login ID) |
| alarmType | String | Yes | Filtered alarm types, separated by commas |

Development Notes

By default, items that need to be alarmed are selected. If you don't want to alarm certain types, you need to uncheck them.

Request Result

Success:
```json
{
"success": "true",
"msg": 1,
"filterObj": ",0,3,7," // Filtered alarm types
}
```

## 19. Get Filtered Alarm Number

Interface Name: GetPushAlarmSwitch
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=GetPushAlarmSwitch&mds=314dc945b0ea498b81b6de21f47f178e

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |

Development Notes

Query the filtered alarm type numbers.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
[
0,
3,
7
]
]
}
```

## 20. Get Alarm Details

Interface Name: alarmOperation
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=alarmOperation&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=1,2,3,4,6,7,8,9,10,11,14,38&enterprise_id=e0329898-4eaa-4612-9f69-00986998d89b&userid=72b92fe0-34ca-483b-b0f9-27e4c7ee3d92&beginTime=1587916800000&endTime=1587951883000

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| alarmType | String | Yes | Alarm Type |
| enterprise_id | Guid | Yes | Unit ID (Login ID) |
| userid | Guid | Yes | Device ID |
| beginTime | Long | Yes | Utc timestamp milliseconds (start time) |
| endTime | Long | Yes | Utc timestamp milliseconds (end time) |

Development Notes

See the 6th interface for return result notes.

Request Result

Success:
```json
{
id: "efe0fb98-7e86-4a94-bdab-642ae07898cc",
fullname: "A (press)",
classify: 38,
lon: 117.203048,
lat: 31.926648,
addtime: "2020/04/27 08:31:57",
ptime: "2020/04/27 08:31:44",
}
```

## 21. Device Change Password

Interface Name: modifyUserPwd
Request Method: HTTP GET
Request Example: (Not provided in OCR content)

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| pwd | Guid | Yes | New Password |
| oldPwd | Guid | Yes | Old Password |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

## 22. Send Command History Record

Interface Name: GetSendCmdList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetSendCmdList&mds=a544e41669d6455f84b862f666eaffdd&macid=027045307931&count=&_t=1614219663391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| count | int | No | Number of entries |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
```



```json
{
"sim": 22, // SIM card
"precision": 23 // Accuracy error (Lbs/WiFi) When the value is 10, it means GPS positioning, no accuracy
// Actual value, here is one more digit, whether to follow, whether to authorize, no use
},
"records": [
[
1505987223000, // Device positioning time (utc timestamp)
"Device Name",
113.8272056,
22.6993799,
113.8272056,
22.6993799,
1505987173965,
1506325381478,
"0",
"1010000",
0,
"Device Number",
"a12e71f7a9b248df91fcbc65cbd9649",
1,
"01",
1506399055758,
"T3",
1516233600000,
"0",
"2435.99,0,0,,0,0,0,0,0,0,0,0,0,0,0,0",
0,
"",
"",
10
]
],
"groups": []
}
]
}
```

## Status and Statenumber Descriptions

**Status (description):** composed of eight consecutive characters

*   **First digit:** acc status (accState 1 on 0 off)
*   **Second digit:** device self-defense status (ProtectState 1 armed 0 disarmed)
*   **Third digit:** oil/electric (oilState 1 on 0 off)
*   **Fourth digit:** charging status ()
*   **Fifth digit:** door status (1 open 0 closed)
*   **Sixth digit:** positioning status (1 positioned 0 unpositioned) [meaningless]
*   **Seventh digit:** main power status (ElecState)
*   **Eighth digit:** platform arming status (defend) [meaningless]
*   **Ninth digit:** platform stealth status (gpsOnOff) (1 for stealth)

**Statenumber (description):** composed of sixteen consecutive comma-separated characters

*   **First digit:** mileage (mil)
*   **Second digit:** oil volume (oil)
*   **Third digit:** weight (weight)
*   **Fourth digit:** temperature 1 (tempc)
*   **Fifth digit:** backup battery power (betteryV), there are two ways to represent power: 1 is percentage, 2 is V (volt)
    *   When power is greater than 100, -100 to remove V unit, less than 100 as percentage
*   **Sixth digit:** main power voltage (powerV), unit: V
*   **Seventh digit:** GPS count (gpscount)
*   **Eighth digit:** gsm signal strength (gsmlevel)
*   **Ninth digit:** forward/reverse (ClockwiseState)
*   **Tenth digit:** vehicle status (VehicleState)
*   **Eleventh digit:** lock count (lockcnt)
*   **Twelfth digit:** temperature 2 (temp1)
*   **Thirteenth digit:** temperature 3 (temp2)
*   **Fourteenth digit:** temperature 4 (temp3)
*   **Fifteenth digit:** height (height)
*   **Sixteenth digit:** positioning signal type (SignalType) 0: Beidou GPS 1: Wifi positioning 16: Base station positioning
*   **Seventeenth digit:** step number (StepNumber)
*   **Eighteenth digit:** VIN

## Development Supplement:

```json
{
"key": {
"sys_time":0,
"user_name":1,
"jingdu":2,
"weidu":3,
"ljingdu":4,
"lweidu":5,
"datetime":6,
"heart_time":7,
"su":8,
"status":9,
"hangxiang":10,
"sim_id":11,
"user_id":12,
"sale_type":13,
"iconType":14,
"server_time":15,
"product_type":16,
"expire_date":17,
"group_id":18,
"statenumber":19,
"electric":20,
"describe":21,
"sim":22,
"precision":23,
"isFollow":24
},
"records": [
[
1557712988,
"59号",
121.527837,
31.100259,
121.527837,
31.100259,
1557712988000,
1557713134721,
"0",
"10000010",
124.0,
"8629640236329",
"fdd70e9ebc6c4559aca661e49e",
0,
"22",
1557713139056,
"GS05B",
2204150400000,
"0",
"4.59,0,0,,6,0,12,4,0,0,0,0,0,0,0,0,",
6.0,
"ICCID:89860412101",
"",
10, // When the value is 10, it means GPS positioning, no (base station/wifi) accuracy
"0" // Whether to follow [no use]
]
],
"groups": []
}
```

**Note a logic:** first judge whether it has expired, then judge whether it is positioned, then judge which positioning method it is, and finally organize the corresponding display effect.



```json
{
"sim": 22, // SIM card
"precision": 23 // Accuracy error (Lbs/WiFi) When the value is 10, it means GPS positioning, no accuracy
// Actual value, here is one more digit, whether to follow, whether to authorize, no use
},
"records": [
[
1505987223000, // Device positioning time (utc timestamp)
"Device Name",
113.8272056,
22.6993799,
113.8272056,
22.6993799,
1505987173965,
1506325381478,
"0",
"1010000",
0,
"Device Number",
"a12e71f7a9b248df91fcbc65cbd9649",
1,
"01",
1506399055758,
"T3",
1516233600000,
"0",
"2435.99,0,0,,0,0,0,0,0,0,0,0,0,0,0,0",
0,
"",
"",
10
]
],
"groups": []
}
]
}
```

## Status and Statenumber Descriptions

**Status (description):** composed of eight consecutive characters

*   **First digit:** acc status (accState 1 on 0 off)
*   **Second digit:** device self-defense status (ProtectState 1 armed 0 disarmed)
*   **Third digit:** oil/electric (oilState 1 on 0 off)
*   **Fourth digit:** charging status ()
*   **Fifth digit:** door status (1 open 0 closed)
*   **Sixth digit:** positioning status (1 positioned 0 unpositioned) [meaningless]
*   **Seventh digit:** main power status (ElecState)
*   **Eighth digit:** platform arming status (defend) [meaningless]
*   **Ninth digit:** platform stealth status (gpsOnOff) (1 for stealth)

**Statenumber (description):** composed of sixteen consecutive comma-separated characters

*   **First digit:** mileage (mil)
*   **Second digit:** oil volume (oil)
*   **Third digit:** weight (weight)
*   **Fourth digit:** temperature 1 (tempc)
*   **Fifth digit:** backup battery power (betteryV), there are two ways to represent power: 1 is percentage, 2 is V (volt)
    *   When power is greater than 100, -100 to remove V unit, less than 100 as percentage
*   **Sixth digit:** main power voltage (powerV), unit: V
*   **Seventh digit:** GPS count (gpscount)
*   **Eighth digit:** gsm signal strength (gsmlevel)
*   **Ninth digit:** forward/reverse (ClockwiseState)
*   **Tenth digit:** vehicle status (VehicleState)
*   **Eleventh digit:** lock count (lockcnt)
*   **Twelfth digit:** temperature 2 (temp1)
*   **Thirteenth digit:** temperature 3 (temp2)
*   **Fourteenth digit:** temperature 4 (temp3)
*   **Fifteenth digit:** height (height)
*   **Sixteenth digit:** positioning signal type (SignalType) 0: Beidou GPS 1: Wifi positioning 16: Base station positioning
*   **Seventeenth digit:** step number (StepNumber)
*   **Eighteenth digit:** VIN

## Development Supplement:

```json
{
"key": {
"sys_time":0,
"user_name":1,
"jingdu":2,
"weidu":3,
"ljingdu":4,
"lweidu":5,
"datetime":6,
"heart_time":7,
"su":8,
"status":9,
"hangxiang":10,
"sim_id":11,
"user_id":12,
"sale_type":13,
"iconType":14,
"server_time":15,
"product_type":16,
"expire_date":17,
"group_id":18,
"statenumber":19,
"electric":20,
"describe":21,
"sim":22,
"precision":23,
"isFollow":24
},
"records": [
[
1557712988,
"59号",
121.527837,
31.100259,
121.527837,
31.100259,
1557712988000,
1557713134721,
"0",
"10000010",
124.0,
"8629640236329",
"fdd70e9ebc6c4559aca661e49e",
0,
"22",
1557713139056,
"GS05B",
2204150400000,
"0",
"4.59,0,0,,6,0,12,4,0,0,0,0,0,0,0,0,",
6.0,
"ICCID:89860412101",
"",
10, // When the value is 10, it means GPS positioning, no (base station/wifi) accuracy
"0" // Whether to follow [no use]
]
],
"groups": []
}
```

**Note a logic:** first judge whether it has expired, then judge whether it is positioned, then judge which positioning method it is, and finally organize the corresponding display effect.




## 16. Get Subordinate Unit Device List

Interface Name: getDeviceListByCustomId
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=GetDeviceListByCustomId&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |
| mapType | String | Yes | Map Type |

Development Notes

Get the device list of subordinate units.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // See latest single position
"sys_time": 0,
"user_name": 1,
"jingdu": 2,
"weidu": 3,
"ljingdu": 4,
"lweidu": 5,
"datetime": 6,
"heart_time": 7,
"su": 8,
"status": 9,
"hangxiang": 10,
"sim_id": 11,
"user_id": 12,
"sale_type": 13,
"iconType": 14,
"server_time": 15,
"product_type": 16,
"expire_date": 17,
"group_id": 18,
"statenumber": 19,
"electric": 20,
"describe": 21,
"sim": 22,
"precision": 23,
"isFollow": 24
},
"records": [
[1482596907000, "601603080377", 114.231935, 22.667007,
114.231935, 22.667007, 1482591004402, 1482596908894, "0", "00000100", 134.0, "601603080377", "2f45ed3a309744c5b9da11cc5bd3d5dd", 0, "22", 1551926835558, "DXMKT", 2141959763770, "1830a1e7cf8a4790b10e3bab2e7c2406", "813312.57,0,0,0,0,0,0,0,0,0,0,0,0,0,0", "1234458373", 10, "0"],
[0, "DXMKT7908511", 0.0, 0.0, 0.0, 0.0, 0, 0, "-9", "00000000", 0.0, "867273027908511", "559d5b39f46c4790b729d2a86f5bef31", 0, "01", 1551926835558, "DXMKT", 1542240000000, "0", "0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0", 10, "0"]
],
"groups": [
"followCount": 0, // Number of followers
"deviceCount": 3 // Number of devices
]
}
]
}
```

## 17. Bind Device Interface [Reserved]

Interface Name: MoveDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=MoveDevice&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d&macid=027028656666

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID (Self-written) |
| macid | String | Yes | Device Number |

Development Notes

If you have a registered account, you need to add another device to your account.

Request Result

Success:
```json
------
```

## 18. Set Alarm Filter [Common for Device Registration and Account Login]

Interface Name: setFilterAlarmInfo
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=setFilterAlarmInfo&custid=754a5a28-8f24-47ff-908f-c363663db702&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=0,3,7

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID (Login ID) |
| alarmType | String | Yes | Filtered alarm types, separated by commas |

Development Notes

By default, items that need to be alarmed are selected. If you don't want to alarm certain types, you need to uncheck them.

Request Result

Success:
```json
{
"success": "true",
"msg": 1,
"filterObj": ",0,3,7," // Filtered alarm types
}
```

## 19. Get Filtered Alarm Number

Interface Name: GetPushAlarmSwitch
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=GetPushAlarmSwitch&mds=314dc945b0ea498b81b6de21f47f178e

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |

Development Notes

Query the filtered alarm type numbers.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
[
0,
3,
7
]
]
}
```

## 20. Get Alarm Details

Interface Name: alarmOperation
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=alarmOperation&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=1,2,3,4,6,7,8,9,10,11,14,38&enterprise_id=e0329898-4eaa-4612-9f69-00986998d89b&userid=72b92fe0-34ca-483b-b0f9-27e4c7ee3d92&beginTime=1587916800000&endTime=1587951883000

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| alarmType | String | Yes | Alarm Type |
| enterprise_id | Guid | Yes | Unit ID (Login ID) |
| userid | Guid | Yes | Device ID |
| beginTime | Long | Yes | Utc timestamp milliseconds (start time) |
| endTime | Long | Yes | Utc timestamp milliseconds (end time) |

Development Notes

See the 6th interface for return result notes.

Request Result

Success:
```json
{
id: "efe0fb98-7e86-4a94-bdab-642ae07898cc",
fullname: "A (press)",
classify: 38,
lon: 117.203048,
lat: 31.926648,
addtime: "2020/04/27 08:31:57",
ptime: "2020/04/27 08:31:44",
status: 0,
speed: 0,
macid: "09103****855",
userid: "72b92fe0-34ca-483b-b0f9-27e4c7ee3992",
macname: "JT888",
NumberId: 3,
Notea: ""
}
]
}
```

## 21. Device Change Password

Interface Name: modifyUserPwd
Request Method: HTTP GET
Request Example: (Not provided in OCR content)

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| pwd | Guid | Yes | New Password |
| oldPwd | Guid | Yes | Old Password |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

## 22. Send Command History Record

Interface Name: GetSendCmdList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetSendCmdList&mds=a544e41669d6455f84b862f666eaffdd&macid=027045307931&count=&_t=1614219663391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| count | int | No | Number of entries |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
// ... (truncated content)
]
}
```

## 23. Get Device List (with status)

Interface Name: getDeviceList
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getDeviceList&mds=3f8a8c56a7df4be0b7561e44c13fa069

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | No | Subordinate Unit ID [Reserved] |

Development Notes

Retrieves device information directly under your account (does not include subordinates).

Request Result

Success: offline 0 offline 1 online stationary 2 online moving

## 24. Get Latest Position (Single Device) - Detailed

Interface Name: getUserAndGpsInfoByIDsUtc
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?mapType=BAIDU&mds=0e41931ac2654d2e839d29847c385ad8&method=getUserAndGpsInfoByIDsUtc&option=cn&user_id=c0095ae6-db7d-4932-8cb5-5b1531ed3637

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| mapType | String | Yes | Map Type "BAIDU" |
| option | String | Yes | Language "cn" |
| user_id | Guid | Yes | Device ID |

Development Notes

How to determine if it's online?
Compare signal time (No. 7) and server time (No. 15). If the difference is 25 minutes, it's offline. If less than 25 minutes, it's online. If speed is 0, it's stationary; if not 0, it's moving.

How to calculate how long it has been stationary or offline?
Stationary time = server_time (system time) - datetime (update time)
Offline time = server_time (system time) - heart_time (device heartbeat time)
Note: The calculated values are in milliseconds. Divide by 1000 to get seconds. App development and h5 both have conversion methods; search online.

Unactivated judgment: Speed equals -9 AND offline time (server_time - heart_time) > 25 minutes
Expired judgment: server_time (system time) - expire_date (expiration time, No. 17)

Request Result

Success:
The data in 'key' is explanatory. Each value in each array of the 'records' array is what you really need. For example, 'status' corresponds to "Status [see status below]". Generally used as: records[0][status]. Also, if the 'records' array is empty, it means this account has no devices.
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // This key is a detailed note for the 'records' group below
"sys_time": 0, // Device positioning time
"user_name": 1, // Device name
"jingdu": 2, // Longitude (GPS positioning)
"weidu": 3, // Latitude (GPS positioning)
"ljingdu": 4, // Base station longitude (use this longitude/latitude for base station or WiFi positioning)
"lweidu": 5, // Base station latitude (use this longitude/latitude for base station or WiFi positioning)
"datetime": 6, // Data update time (server reception time)
"heart_time": 7, // Device heartbeat time (signal time)
"su": 8, // Speed
"status": 9, // Status group [see status table below]
"hangxiang": 10, // Direction
"sim_id": 11, // Device number
"user_id": 12, // Device ID
"sale_type": 13, // Sales type - no use
"iconType": 14, // Icon
"server_time": 15, // System time (current time)
"product_type": 16, // Device type
"expire_date": 17, // Expiration time
"group_id": 18, // Monitoring group ID
"statenumber": 19, // Information group [see status table below]
"electric": 20,
"describe": 21, // Device description information (usually stores some operating parameters uploaded by the device)
"sim": 22, // SIM card
"precision": 23 // Accuracy error (Lbs/WiFi) When the value is 10, it means GPS positioning, no accuracy
// Actual value, here is one more digit, whether to follow, whether to authorize, no use
},
"records": [
[
1505987223000, // Device positioning time (utc timestamp)
"Device Name",
113.8272056,
22.6993799,
113.8272056,
22.6993799,
1505987173965,
1506325381478,
"0",
"1010000",
0,
"Device Number",
"a12e71f7a9b248df91fcbc65cbd9649",
1,
"01",
1506399055758,
"T3",
1516233600000,
"0",
"2435.99,0,0,,0,0,0,0,0,0,0,0,0,0,0,0",
0,
"",
"",
10
]
]
}
]
}
```

## Status and Statenumber Descriptions (Continued)

**Status (description):** composed of eight consecutive characters

*   **First digit:** acc status (accState 1 on 0 off)
*   **Second digit:** device self-defense status (ProtectState 1 armed 0 disarmed)
*   **Third digit:** oil/electric (oilState 1 on 0 off)
*   **Fourth digit:** charging status ()
*   **Fifth digit:** door status (1 open 0 closed)
*   **Sixth digit:** positioning status (1 positioned 0 unpositioned) [meaningless]
*   **Seventh digit:** main power status (ElecState)
*   **Eighth digit:** platform arming status (defend) [meaningless]
*   **Ninth digit:** platform stealth status (gpsOnOff) (1 for stealth)

**Statenumber (description):** composed of sixteen consecutive comma-separated characters

*   **First digit:** mileage (mil)
*   **Second digit:** oil volume (oil)
*   **Third digit:** weight (weight)
*   **Fourth digit:** temperature 1 (tempc)
*   **Fifth digit:** backup battery power (betteryV), there are two ways to represent power: 1 is percentage, 2 is V (volt)
    *   When power is greater than 100, -100 to remove V unit, less than 100 as percentage
*   **Sixth digit:** main power voltage (powerV), unit: V
*   **Seventh digit:** GPS count (gpscount)
*   **Eighth digit:** gsm signal strength (gsmlevel)
*   **Ninth digit:** forward/reverse (ClockwiseState)
*   **Tenth digit:** vehicle status (VehicleState)
*   **Eleventh digit:** lock count (lockcnt)
*   **Twelfth digit:** temperature 2 (temp1)
*   **Thirteenth digit:** temperature 3 (temp2)
*   **Fourteenth digit:** temperature 4 (temp3)
*   **Fifteenth digit:** height (height)
*   **Sixteenth digit:** positioning signal type (SignalType) 0: Beidou GPS 1: Wifi positioning 16: Base station positioning
*   **Seventeenth digit:** step number (StepNumber)
*   **Eighteenth digit:** VIN

## Development Supplement (Continued):

```json
{
"key": {
"sys_time":0,
"user_name":1,
"jingdu":2,
"weidu":3,
"ljingdu":4,
"lweidu":5,
"datetime":6,
"heart_time":7,
"su":8,
"status":9,
"hangxiang":10,
"sim_id":11,
"user_id":12,
"sale_type":13,
"iconType":14,
"server_time":15,
"product_type":16,
"expire_date":17,
"group_id":18,
"statenumber":19,
"electric":20,
"describe":21,
"sim":22,
"precision":23,
"isFollow":24
},
"records": [
[
1557712988,
"59号",
121.527837,
31.100259,
121.527837,
31.100259,
1557712988000,
1557713134721,
"0",
"10000010",
124.0,
"8629640236329",
"fdd70e9ebc6c4559aca661e49e",
0,
"22",
1557713139056,
"GS05B",
2204150400000,
"0",
"4.59,0,0,,6,0,12,4,0,0,0,0,0,0,0,0,",
6.0,
"ICCID:89860412101",
"",
10, // When the value is 10, it means GPS positioning, no (base station/wifi) accuracy
"0" // Whether to follow [no use]
]
],
"groups": []
}
```

**Note a logic:** first judge whether it has expired, then judge whether it is positioned, then judge which positioning method it is, and finally organize the corresponding display effect.




## 16. Get Subordinate Unit Device List

Interface Name: getDeviceListByCustomId
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getDeviceListByCustomId&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |
| mapType | String | No | Map Type (Default Baidu "BAIDU") |

Development Notes

Get the device list of subordinate units.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // See latest single position
"sys_time": 0,
"user_name": 1,
"jingdu": 2,
"weidu": 3,
"ljingdu": 4,
"lweidu": 5,
"datetime": 6,
"heart_time": 7,
"su": 8,
"status": 9,
"hangxiang": 10,
"sim_id": 11,
"user_id": 12,
"sale_type": 13,
"iconType": 14,
"server_time": 15,
"product_type": 16,
"expire_date": 17,
"group_id": 18,
"statenumber": 19,
"electric": 20,
"describe": 21,
"sim": 22,
"precision": 23,
"isFollow": 24
},
"records": [
[1482596907000, "601603080377", 114.231935, 22.667007,
114.231935, 22.667007, 1482591004402, 1482596908894, "0", "00000100", 134.0, "601603080377", "2f45ed3a309744c5b9da11cc5bd3d5dd", 0, "22", 1551926835558, "DXMKT", 2141959763770, "1830a1e7cf8a4790b10e3bab2e7c2406", "813312.57,0,0,0,0,0,0,0,0,0,0,0,0,0,0", "1234458373", 10, "0"],
[0, "DXMKT7908511", 0.0, 0.0, 0.0, 0.0, 0, 0, "-9", "00000000", 0.0, "867273027908511", "559d5b39f46c4790b729d2a86f5bef31", 0, "01", 1551926835558, "DXMKT", 1542240000000, "0", "0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0", 10, "0"]
],
"groups": [
"followCount": 0, // Number of followers
"deviceCount": 3 // Number of devices
]
}
]
}
```

## 17. Bind Device Interface [Reserved]

Interface Name: MoveDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=MoveDevice&mds=930942815323449093b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d&macid=027028656666

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID (Self-written) |
| macid | String | Yes | Device Number |

Development Notes

If you have a registered account, you need to add another device to your account.

Request Result

Success:
```json
------
```

## 18. Set Alarm Filter [Common for Device Registration and Account Login]

Interface Name: setFilterAlarmInfo
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=setFilterAlarmInfo&custid=754a5a28-8f24-47ff-908f-c363663db702&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=0,3,7

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID (Login ID) |
| alarmType | String | Yes | Filtered alarm types, separated by commas |

Development Notes

By default, items that need to be alarmed are selected. If you don\'t want to alarm certain types, you need to uncheck them.

Request Result

Success:
```json
{
"success": "true",
"msg": 1,
"filterObj": ",0,3,7," // Filtered alarm types
}
```

## 19. Get Filtered Alarm Number

Interface Name: GetPushAlarmSwitch
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=GetPushAlarmSwitch&mds=314dc945b0ea498b81b6de21f47f178e

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |

Development Notes

Query the filtered alarm type numbers.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
[
0,
3,
7
]
]
}
```

## 20. Get Alarm Details

Interface Name: alarmOperation
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=alarmOperation&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=1,2,3,4,6,7,8,9,10,11,14,38&enterprise_id=e0329898-4eaa-4612-9f69-00986998d89b&userid=72b92fe0-34ca-483b-b0f9-27e4c7ee3d92&beginTime=1587916800000&endTime=1587951883000

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| alarmType | String | Yes | Alarm Type |
| enterprise_id | Guid | Yes | Unit ID (Login ID) |
| userid | Guid | Yes | Device ID |
| beginTime | Long | Yes | Utc timestamp milliseconds (start time) |
| endTime | Long | Yes | Utc timestamp milliseconds (end time) |

Development Notes

See the 6th interface for return result notes.

Request Result

Success:
```json
{
id: "efe0fb98-7e86-4a94-bdab-642ae07898cc",
fullname: "A (press)",
classify: 38,
lon: 117.203048,
lat: 31.926648,
addtime: "2020/04/27 08:31:57",
ptime: "2020/04/27 08:31:44",
status: 0,
speed: 0,
macid: "09103****855",
userid: "72b92fe0-34ca-483b-b0f9-27e4c7ee3992",
macname: "JT888",
NumberId: 3,
Notea: ""
}
]
}
```

## 21. Device Change Password

Interface Name: modifyUserPwd
Request Method: HTTP GET
Request Example: (Not provided in OCR content)

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| pwd | Guid | Yes | New Password |
| oldPwd | Guid | Yes | Old Password |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

## 22. Send Command History Record

Interface Name: GetSendCmdList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetSendCmdList&mds=a544e41669d6455f84b862f666eaffdd&macid=027045307931&count=&_t=1614219663391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| count | int | No | Number of entries |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
// ... (truncated content)
]
}
```

## 23. Get Device List (with status)

Interface Name: getDeviceList
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getDeviceList&mds=3f8a8c56a7df4be0b7561e44c13fa069

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | No | Subordinate Unit ID [Reserved] |

Development Notes

Retrieves device information directly under your account (does not include subordinates).

Request Result

Success: offline 0 offline 1 online stationary 2 online moving

## 24. Get Latest Position (Single Device) - Detailed

Interface Name: getUserAndGpsInfoByIDsUtc
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?mapType=BAIDU&mds=0e41931ac2654d2e839d29847c385ad8&method=getUserAndGpsInfoByIDsUtc&option=cn&user_id=c0095ae6-db7d-4932-8cb5-5b1531ed3637

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| mapType | String | Yes | Map Type "BAIDU" |
| option | String | Yes | Language "cn" |
| user_id | Guid | Yes | Device ID |

Development Notes

How to determine if it\'s online?
Compare signal time (No. 7) and server time (No. 15). If the difference is 25 minutes, it\'s offline. If less than 25 minutes, it\'s online. If speed is 0, it\'s stationary; if not 0, it\'s moving.

How to calculate how long it has been stationary or offline?
Stationary time = server_time (system time) - datetime (update time)
Offline time = server_time (system time) - heart_time (device heartbeat time)
Note: The calculated values are in milliseconds. Divide by 1000 to get seconds. App development and h5 both have conversion methods; search online.

Unactivated judgment: Speed equals -9 AND offline time (server_time - heart_time) > 25 minutes
Expired judgment: server_time (system time) - expire_date (expiration time, No. 17)

Request Result

Success:
The data in \'key\' is explanatory. Each value in each array of the \'records\' array is what you really need. For example, \'status\' corresponds to "Status [see status below]". Generally used as: records[0][status]. Also, if the \'records\' array is empty, it means this account has no devices.
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // This key is a detailed note for the \'records\' group below
"sys_time": 0, // Device positioning time
"user_name": 1, // Device name
"jingdu": 2, // Longitude (GPS positioning)
"weidu": 3, // Latitude (GPS positioning)
"ljingdu": 4, // Base station longitude (use this longitude/latitude for base station or WiFi positioning)
"lweidu": 5, // Base station latitude (use this longitude/latitude for base station or WiFi positioning)
"datetime": 6, // Data update time (server reception time)
"heart_time": 7, // Device heartbeat time (signal time)
"su": 8, // Speed
"status": 9, // Status group [see status table below]
"hangxiang": 10, // Direction
"sim_id": 11, // Device number
"user_id": 12, // Device ID
"sale_type": 13, // Sales type - no use
"iconType": 14, // Icon
"server_time": 15, // System time (current time)
"product_type": 16, // Device type
"expire_date": 17, // Expiration time
"group_id": 18, // Monitoring group ID
"statenumber": 19, // Information group [see status table below]
"electric": 20,
"describe": 21, // Device description information (usually stores some operating parameters uploaded by the device)
"sim": 22, // SIM card
"precision": 23 // Accuracy error (Lbs/WiFi) When the value is 10, it means GPS positioning, no accuracy
// Actual value, here is one more digit, whether to follow, whether to authorize, no use
},
"records": [
[
1505987223000, // Device positioning time (utc timestamp)
"Device Name",
113.8272056,
22.6993799,
113.8272056,
22.6993799,
1505987173965,
1506325381478,
"0",
"1010000",
0,
"Device Number",
"a12e71f7a9b248df91fcbc65cbd9649",
1,
"01",
1506399055758,
"T3",
1516233600000,
"0",
"2435.99,0,0,,0,0,0,0,0,0,0,0,0,0,0,0",
0,
"",
"",
10
]
]
}
]
}
```

## Status and Statenumber Descriptions (Continued)

**Status (description):** composed of eight consecutive characters

*   **First digit:** acc status (accState 1 on 0 off)
*   **Second digit:** device self-defense status (ProtectState 1 armed 0 disarmed)
*   **Third digit:** oil/electric (oilState 1 on 0 off)
*   **Fourth digit:** charging status ()
*   **Fifth digit:** door status (1 open 0 closed)
*   **Sixth digit:** positioning status (1 positioned 0 unpositioned) [meaningless]
*   **Seventh digit:** main power status (ElecState)
*   **Eighth digit:** platform arming status (defend) [meaningless]
*   **Ninth digit:** platform stealth status (gpsOnOff) (1 for stealth)

**Statenumber (description):** composed of sixteen consecutive comma-separated characters

*   **First digit:** mileage (mil)
*   **Second digit:** oil volume (oil)
*   **Third digit:** weight (weight)
*   **Fourth digit:** temperature 1 (tempc)
*   **Fifth digit:** backup battery power (betteryV), there are two ways to represent power: 1 is percentage, 2 is V (volt)
    *   When power is greater than 100, -100 to remove V unit, less than 100 as percentage
*   **Sixth digit:** main power voltage (powerV), unit: V
*   **Seventh digit:** GPS count (gpscount)
*   **Eighth digit:** gsm signal strength (gsmlevel)
*   **Ninth digit:** forward/reverse (ClockwiseState)
*   **Tenth digit:** vehicle status (VehicleState)
*   **Eleventh digit:** lock count (lockcnt)
*   **Twelfth digit:** temperature 2 (temp1)
*   **Thirteenth digit:** temperature 3 (temp2)
*   **Fourteenth digit:** temperature 4 (temp3)
*   **Fifteenth digit:** height (height)
*   **Sixteenth digit:** positioning signal type (SignalType) 0: Beidou GPS 1: Wifi positioning 16: Base station positioning
*   **Seventeenth digit:** step number (StepNumber)
*   **Eighteenth digit:** VIN

## Development Supplement (Continued):

```json
{
"key": {
"sys_time":0,
"user_name":1,
"jingdu":2,
"weidu":3,
"ljingdu":4,
"lweidu":5,
"datetime":6,
"heart_time":7,
"su":8,
"status":9,
"hangxiang":10,
"sim_id":11,
"user_id":12,
"sale_type":13,
"iconType":14,
"server_time":15,
"product_type":16,
"expire_date":17,
"group_id":18,
"statenumber":19,
"electric":20,
"describe":21,
"sim":22,
"precision":23,
"isFollow":24
},
"records": [
[
1557712988,
"59号",
121.527837,
31.100259,
121.527837,
31.100259,
1557712988000,
1557713134721,
"0",
"10000010",
124.0,
"8629640236329",
"fdd70e9ebc6c4559aca661e49e",
0,
"22",
1557713139056,
"GS05B",
2204150400000,
"0",
"4.59,0,0,,6,0,12,4,0,0,0,0,0,0,0,0,",
6.0,
"ICCID:89860412101",
"",
10, // When the value is 10, it means GPS positioning, no (base station/wifi) accuracy
"0" // Whether to follow [no use]
]
]
}
```

**Note a logic:** first judge whether it has expired, then judge whether it is positioned, then judge which positioning method it is, and finally organize the corresponding display effect.




## 16. Get Subordinate Unit Device List

Interface Name: getDeviceListByCustomId
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getDeviceListByCustomId&mds=9309428153234490931b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Subordinate Unit ID |
| mapType | String | No | Map Type (Default Baidu "BAIDU") |

Development Notes

Retrieves the device list of subordinate units.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
"key": { // See latest single position
"sys_time": 0,
"user_name": 1,
"jingdu": 2,
"weidu": 3,
"ljingdu": 4,
"lweidu": 5,
"datetime": 6,
"heart_time": 7,
"su": 8,
"status": 9,
"hangxiang": 10,
"sim_id": 11,
"user_id": 12,
"sale_type": 13,
"iconType": 14,
"server_time": 15,
"product_type": 16,
"expire_date": 17,
"group_id": 18,
"statenumber": 19,
"electric": 20,
"describe": 21,
"sim": 22,
"precision": 23,
"isFollow": 24
},
"records": [
[1482596907000, "601603080377", 114.231935, 22.667007,
114.231935, 22.667007, 1482591004402, 1482596908894, "0", "00000100", 134.0, "601603080377", "2f45ed3a309744c5b9da11cc5bd3d5dd", 0, "22", 1551926835558, "DXMKT", 2141959763770, "1830a1e7cf8a4790b10e3bab2e7c2406", "813312.57,0,0,,0,0,5,26,0,0,0,0,0,0,0,0,", 0.0, "", "1234458373", 10, "0"],
[1511853204362, "902B75072918", 114.223921, 22.665438,
114.223921, 22.665438, 1511853204362, 1511853767987, "0", "00000000", 0.0,
"391517075072918", "6a33f9dc7418430ba5388dba4cdc9f33", 0, "22",
1551926835558, "902B", 1544515200000, "0", "0,0,0,,6,0,0,100,0,0,0,0,0,0,0,16,",
6.0, "", "", 550, "0"],
[0, "DXMKT7908511", 0.0, 0.0, 0.0, 0.0, 0, 0, "-9", "00000000", 0.0,
"867273027908511", "559d5b39f46c4790b729d2a86f5bef31", 0, "01",
1551926835558, "DXMKT", 1542240000000, "0", "0,0,0,,0,0,0,0,0,0,0,0,0,0,0,0,",
0.0, "", "", 10, "0"]
],
"groups": [],
"followCount": 0, // Number of followers
"deviceCount": 3 // Number of devices
}
]
}
```

## 17. Bind Device Interface [Reserved]

Interface Name: MoveDevice
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=MoveDevice&mds=9309428153234490931b3923d170cd40&id=334f2e92-787d-45e8-bf5c-3b500a8c787d&macid=027028656666

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID (Self-written) |
| macid | String | Yes | Device Number |

Development Notes

If you have a registered account, you need to add another device to your account.

Request Result

Success:
```json
------
```




## 18. Set Alarm Filter [Common for Device Registration and Account Login]

Interface Name: setFilterAlarmInfo
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=setFilterAlarmInfo&custid=754a5a28-8f24-47ff-908f-c363663db702&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=,0,3,7,

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID (Login ID) |
| alarmType | String | Yes | Filtered alarm types, separated by commas |

Development Notes

By default, items that need to be alarmed are selected. If you don't want to alarm certain types, you need to uncheck them.

Request Result

Success:
```json
{
"success": "true",
"msg": 1,
"filterObj": ",0,3,7," // Filtered alarm types
}
```

## 19. Get Filtered Alarm Number

Interface Name: GetPushAlarmSwitch
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=GetPushAlarmSwitch&mds=314dc945b0ea498b81b6de21f47f178e

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |

Development Notes

Query the filtered alarm type numbers.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
[
0,
3,
7
]
]
}
```




## 20. Get Alarm Details

Interface Name: alarmOperation
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDataService.aspx?method=alarmOperation&mds=314dc945b0ea498b81b6de21f47f178e&alarmType=1,2,3,4,6,7,8,9,10,11,14,38&enterprise_id=e0329898-4eaa-4612-9f69-00986998d89b&userid=72b92fe0-34ca-483b-b0f9-27e4c7ee3d92&beginTime=1587916800000&endTime=1587951883000

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| alarmType | String | Yes | Alarm Type |
| enterprise_id | Guid | Yes | Unit ID (Login ID) |
| userid | Guid | Yes | Device ID |
| beginTime | Long | Yes | Utc timestamp milliseconds (start time) |
| endTime | Long | Yes | Utc timestamp milliseconds (end time) |

Development Notes

See the 6th interface for return result notes.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
{
id: "efe0fb98-7e86-4a94-bdab-642ae07898cc",
fullname: "Anhui A (pressed)",
classify: 38,
lon: 117.203048,
lat: 31.926648,
addtime: "2020/04/27 08:31:57",
ptime: "2020/04/27 08:31:44",
// ... (truncated content)
}
]
}
```




status: 0,
speed: 0,
macid: "09103****855",
userid: "72b92fe0-34ca-483b-b0f9-27e4c7ee3992",
macname: "JT808",
NumberId: 3,
Notea: ""
}
]
}
```

## 21. Device Change Password

Interface Name: modifyUserPwd
Request Method: HTTP GET
Request Example: (Not provided in OCR content)

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| pwd | Guid | Yes | New Password |
| oldPwd | Guid | Yes | Old Password |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```




## 22. Send Command History Record

Interface Name: GetSendCmdList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetSendCmdList&mds=a544e41669d6455f84b862f666eaffdd&macid=027045307931&count=&_t=1614219663391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| count | int | No | Number of entries |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [
// ... (truncated content)
]
}
```



```json
{
"Describe": "GETVERSION",//Command word (command type) needs dictionary conversion
"Param": "",//Command content
"IsOK": "Null",//Status
"Msg": "",//Response message
"SendTime": 1614219644177,//Send time
"AddTime": 1614219644192,//Add time
"ExtData": {
"source": "web:web"//Source
}
}
]
}
```

# Recording Related

## 1. Get Multimedia Records (Real-time)

Interface Name: GetMultimediaRecords
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetMultimediaRecords&mds=4708977a492c472288b178ef5ca7f977&target=6020036221&userId=e0329898-4eaa-4612-9f69-00986998d89b&maxTime=0

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| target | String | Yes | Device Number |
| userId | Guid | Yes | Login ID (ID returned by login interface) |
| maxTime | Long | Yes | Maximum time (Utc timestamp milliseconds) |




Development Notes

If maxTime is 0, it retrieves the 50 most recent records. If maxTime is a specific time, it retrieves all records between that time and the current time.

Returned Data:

*   **Id:** Message ID
*   **UserId:** Account ID
*   **Target:** Device number or User ID or Channel ID
*   **Content:** Content
*   **FileName:** File name
*   **Url:** File address
*   **InfoType:** Message type [0: voice, 1: video, 2: image, 3: text]
*   **IsUser:** Whether sent by user, 0 means uploaded by device
*   **Addtime:** Add time

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```




## 2. Get Voice History (Pagination)

Interface Name: GetVoiceHistory
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=GetVoiceHistory&mds=4708977a492c472288b178ef5ca7f977&macid=6020036221&loingId=3975226d-d106-47c1-8cc5-17ebc42818bb&minTime=0&count=10

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |
| loingId | Guid | Yes | Login ID (ID returned by login interface) |
| minTime | Guid | Yes | Minimum time (Utc timestamp milliseconds) |
| count | Int | No | Number of entries (default 20) |

Development Notes

Retrieves `count` number of data entries for the `macid` device after the `minTime` timestamp.

Returned Data:

*   **Id:** Message ID
*   **UserId:** Account ID
*   **Target:** Device number or User ID or Channel ID
*   **Content:** Content
*   **FileName:** File name



*   **Url:** File address
*   **InfoType:** Message type [0: voice, 1: video, 2: image, 3: text]
*   **IsUser:** Whether sent by user, 0 means uploaded by device
*   **Addtime:** Add time

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

## 3. Delete Recording (Supports Multiple)

Interface Name: DeleteSingleVoice
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDataService.aspx?method=DeleteSingleVoice&mds=4708977a492c472288b178ef5ca7f977&ids=c0095ae6-db7d-4932-8cb5-5b1531ed3637,c0095ae6-db7d-4932-8cb5-5b1531ed3638&loingId=3975226d-d106-47c1-8cc5-17ebc42818bb

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| ids | String | Yes | Message ID, recording ID, Guid format, multiple IDs separated by commas |
| loingId | Guid | Yes | Login ID (ID returned by login interface) |




Development Notes

Remember the data format for getting records? The first `Id` field (Guid format) can be used for deletion. For batch deletion, separate multiple IDs with English commas.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

# Round Fence CFEN




## NO.1 Add New Device Circular Fence

Interface Name: CircularFenceSave
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=CircularFenceSave&mds=b19862511d9b46c994360c278b9fa461&FenceName=Test1&MacId=062080801189&Status=1&Lat=22.57&Lng=112.57&Radius=500&MapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| MacId | String | Yes | Device Number (IMEI) |
| MapType | String | Yes | Map Type [See Foreword] |
| FenceName | String | Yes | Fence Name |
| Status | Int | Yes | Fence Status 0.Off/1.Entry/2.Exit/3.Bidirectional |
| Lat | Double | Yes | Latitude |
| Lng | Double | Yes | Longitude |
| Radius | Int | Yes | Radius |



| Height | Int | No | Height |

Interface Description:

None.

Request Result:

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```
Failure:
```json
{}
```

## NO.2 Modify Device Circular Fence

Interface Name: CircularFenceModify
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=CircularFenceModify&mds=b19862511d9b46c994360c278b9fa461&FenceName=I was modified&MacId=865609163017108&Status=0&Lat=22.57&Lng=112.57&Radius=1200&MapType=BAIDU&Id=9bf3165f-616b-45f9-8c56-b5210145893c




| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| MapType | String | Yes | Map Type [See Foreword] |
| FenceName | String | Yes | Fence Name |
| Status | Int | Yes | Fence Status 0.Off/1.Entry/2.Exit/3.Bidirectional |
| Lat | Double | Yes | Latitude |
| Lng | Double | Yes | Longitude |
| Radius | Int | Yes | Radius |
| Height | Int | No | Height |
| Id | Guid | Yes | Fence Id |

Interface Description:

None.

Request Result:

Success:
```json
{}
```
Failure:
```json
{}
```




## NO.3 Delete Device Circular Fence

Interface Name: CircularFenceDelete
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=CircularFenceDelete&mds=b19862511d9b46c994360c278b9fa461&MacId=062080801189&Id=4147d849-dbb6-4fd2-833d-6d2f5ffa08a4

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| MacId | String | Yes | Device Number (IMEI) |
| Id | Guid | Yes | Fence Id |

Interface Description:

None.

Request Result:

Success:
```json
{}
```
Failure:
```json
{}
```




## NO.4 Get Circular Fence List (Multiple)

Interface Name: CircularFenceGetList
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=CircularFenceGetList&mds=b19862511d9b46c994360c278b9fa461&MacId=062080801189&MapType=BAIDU

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| MacId | String | Yes | Device Number (IMEI) |
| MapType | String | Yes | Map Type [See Foreword] |

Interface Description:

None.

Request Result:

Success:
```json
{
// ... (truncated content)
}
```



```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"Id": "935bd0f5-c123-485b-b963-afd9f9ada48c", // Fence ID
"UserId": "00000000-0000-0000-0000-000000000000", // No use for now
"MacId": "062080801189", // Device Number
"FenceName": "Test2", // Fence Name
"Status": 1,// Fence Status 0.Off/1.Entry Alarm/2.Exit Alarm/3.Bidirectional
"Lat": 22.566695, // Latitude
"Lng": 112.558606, // Longitude
"Radius": 800, // Radius
"Height": 0, // Height (3D fence, no use for now)
"MapType": null // Map Type, null means original map coordinates
}, {
"Id": "0c0ae4ff-f662-4fb1-adfb-70c9be96231d",
"UserId": "00000000-0000-0000-0000-000000000000",
"MacId": "062080801189",
"FenceName": "Test3",
"Status": 1,
"Lat": 22.566695,
"Lng": 112.558606,
"Radius": 1000,
"Height": 0,
"MapType": null
}]
}
```
Failure:
```json
{}
```

# Authorization Function




## NO.5 Add Device Function Authorization

Interface Name: EmpowerAdd
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=EmpowerAdd&Macid=868120167290847&Authorized=13009400093&mds=3aa6da273ae8402a9291fe3ab1966512&StartTime=1561685418156&EndTime=1561785418156&FunctionList=1,2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |
| mds | String | Yes | Token |
| Authorized | String | Yes | Authorized Person |
| Macid | String | Yes | Device Number |
| StartTime | long | Yes | Start Time (utc milliseconds) |
| EndTime | long | Yes | End Time (utc milliseconds) |
| FunctionList | String | Yes | Function List (cannot be empty) |

Development Notes

FunctionList Details:

1.  Location
2.  Lock/Unlock / Find Car
3.  Start/Stop Engine
4.  Track

A. Cannot authorize oneself.
B. After authorizing others, they cannot re-authorize.
C. Error code 1007 means cannot operate on oneself, error code 50019 means insufficient permissions.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```




## NO.6 Cancel Device Function Authorization

Interface Name: EmpowerDelete
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=EmpowerDelete&EmpowerId=6615f5270d7f44fb9318e18257b8c304&mds=3aa6da273ae8402a9291fe3ab1966512

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |
| mds | String | Yes | Token |
| EmpowerId | Guid | Yes | Authorization ID |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": []
}
```

## NO.7 Get Authorization List [Owner Identity]

Interface Name: EmpowerGetList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=EmpowerGetList&type=0&mds=3aa6da273ae8402a9291fe3ab1966512




| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |
| mds | String | Yes | Token |
| type | String | No | Type (0 by default to get valid authorizations, 1 to get authorizations including expired ones) |

Development Notes

Retrieves authorization information granted by myself. Recently added:

1.  Authorizer's mobile number/account //CustName
2.  Authorized person's mobile number/account //AuthorizedName
3.  Vehicle Name //VehicleName
4.  License Plate Number //PlateNumber

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"EmpowerId": "6615f5270d7f44fb9318e18257b8c304", // Authorization ID
"CustId": "e9c939f19c064312b29547f0162b4176", // Authorizer ID
"Authorized": "ff1fffe199fd49ea8f31bcc47cbe3f4f", // Authorized person ID
"Macid": "868120167290847", // Device Number
"EquipmentId": "bf06541f33874c00903c15ec2879a4d0",// Device ID
"IsExpire": 0, // Is expired 0 No 1 Yes
"StartTime": 1561685418156, // Start time
"EndTime": 1561785418156, // End time
"FunctionList": [1, 2], // Function list
"AddTime": 1561686728650 // Authorization time
}]
}
```




## NO.8 Get Authorized Person List [Guest Identity]

Interface Name: EmpowerGetAuthorizedList
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=EmpowerGetAuthorizedList&mds=50e8b54c44c24a1fa73a07dac977949a

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |
| mds | String | Yes | Token |

Development Notes

As a guest, get authorized device information and permissions.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"EmpowerId": "1a3d61b377324578ab671568da6c1736",
"CustId": "e9c939f19c064312b29547f0162b4176",
"Authorized": "ff1fffe199fd49ea8f31bcc47cbe3f4f",
"Macid": "868120167290847",
"EquipmentId": "bf06541f33874c00903c15ec2879a4d0",
"IsExpire": 0,
"StartTime": 1561685418156,
"EndTime": 1561785418156,
"FunctionList": [1, 2],
"AddTime": 1561687437463
}]
}
```




# Web End

## 0. Description

The web end is suitable for operators' self-operated platforms, where they build front-end pages on their own servers for users to access. It does not support use with the 18gps platform.

## 1. Add New User (with Permissions)

Interface Name: registerCustomer
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=registerCustomer&mds=368867d0b6cc49af8e2685ff471e4cf2&name=Shanghai%20University%20of%20Science%20and%20Technology&schoolID=754a5a28-8f24-47ff-908f-c363663ab225&loginName=skd&pwd=123456&repwd=123456&grade=2&contact=&phone=&addr=

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| schoolID | Guid | Yes | Parent Unit ID |
| name | String | Yes | Customer Name / Unit Name |
| loginName | String | Yes | Login Account |
| pwd | String | Yes | Password |
| repwd | String | Yes | Confirm Password |
| grade | Int | Yes | Customer Type |
| contact | String | No | Contact Person |
| phone | String | No | Phone |
| addr | String | No | Address |




Development Notes

Pay attention to the `grade` parameter for customer type here. Its value does not have a unified standard and needs to be queried in the server database. If you need to use it, please consult the business department.

Request Result

Success:
```json
{
"id": "cfd2722a-4db6-47ab-974e-d0b6d30a7ecd",// Guid assigned to the new account
"result": 1, // 0 means failure
"success": true// true means success
}
```

## 2. Delete User

Interface Name: deleteEnterprise
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=deleteEnterprise&mds=368867d0b6cc49af8e2685ff471e4cf2&id=cfd2722a-4db6-47ab-974e-d0b6d30a7ecd

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |

Development Notes

Deletion is not possible in the following 2 cases: the user's device list is not empty, or there are other users under the user.

Request Result

Success:
```json
{
"success": "true",
"status": 1
}
```



Failure:
```json
{
"success": "false",
"errorCode": "500",
"errorDescribe": "Object reference not set to an instance of an object.",// User not found
"data": []
}
```

## 2. Get User List

Interface Name: GetCustomTreeById
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=GetCustomTreeById&mds=31532675bc4841ceacf0b1c2ed874946&id=e9c939f1-9c06-4312-b295-47f0162b4176

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"id": "3dfca267-77e5-4933-8a72-e03e1c354d65",// Subordinate unit ID
"deptName": "Looking back a thousand times without smiling (2/2)",// Customer name/Unit name
"deviceCount": 2, // Total devices
"userName": "baidu",// Login account
"pId": "e9c939f1-9c06-4312-b295-47f0162b4176", // Parent ID
"isParent": 0 // Is parent node, 0 no (means no subordinates)
}, {
"id": "7ba648da-ad90-4a34-8788-822bcf666665",
"deptName": "Qiuming Mountain (0/0)",
"deviceCount": 0,
"userName": "qiuqiu",
"pId": "e9c939f1-9c06-4312-b295-47f0162b4176",
"isParent": 0
}, {
"id": "a615be7b-4f73-4267-a686-6ad5902e6ac3",
"deptName": "Watch (1/1)",
"deviceCount": 1,
"userName": "shoubiao",
"pId": "e9c939f1-9c06-4312-b295-47f0162b4176",
"isParent": 0
}]
}
```

## 3. Get User Data

Interface Name: getEnterpriseByID
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=getEnterpriseByID&id=cfd2722a-4db6-47ab-974e-d0b6d30a7ecd&mds=368867d0b6cc49af8e2685ff471e4cf2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| id | Guid | Yes | Unit ID |

Development Notes

None.

Request Result

Success:
```json
{
// ... (truncated content)
}
```



```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"id": "cfd2722a-4db6-47ab-974e-d0b6d30a7ecd",
"name": "Shanghai University of Science and Technology",
"phone": null,
"addr": null,
"contact": null, // Contact person
"loginName": "skd", // Login account
"company": "Shanghai University of Science and Technology", // Customer name/Unit name
"state": "",
"area": "",
"permission": 2, // Tree level
"grade": 2, // Account type
"artificial_person": "skd",
"office_tel": null,
"office_phone": null,
"credit_grade": "0", // None
"status": false, // Whether to receive subordinate alarms
"balance": 0.0, // None
"helpTel": "", // None
"alarmSound": false // Enable alarm sound
}]
}
```

## 4. Modify User Data

Interface Name: modifyEnterprise
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=modifyEnterprise&id=cfd2722a-4db6-47ab-974e-d0b6d30a7ecd&mds=368867d0b6cc49af8e2685ff471e4cf2&company=Beijing%20University%20of%20Science%20and%20Technology&loginName=skd&grade=2&contact=&phone=&addr=

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |



|
id | Guid | Yes | Unit ID |
| loginName | String | Yes | Login Account |
| grade | String | Yes | Customer Type |
| contact | String | No | Contact Person |
| phone | String | No | Phone |
| addr | String | No | Address |
| company | String | No | Customer Name / Unit Name |
| status | Int | No | Whether to receive subordinate alarms |

Development Notes

Note: `loginName` represents the unit name during registration, but in this interface, it represents the login account.

Request Result

Success:
```json
{
"success": true
}
```

## 5. Reset User Password

Interface Name: resetCustomerPWD
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=resetCustomerPWD&mds=368867d0b6cc49af8e2685ff471e4cf2&school_id=cfd2722a-4db6-47ab-974e-d0b6d30a7ecd

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| school_id | Guid | Yes | Unit ID |

Development Notes

There is permission verification here. If `school_id` is not within the token's management scope, the interface is invalid.




Request Result

Success:
```json
{
"success": true,
"Pwd": "123456"
}
```

## 6. Batch Add Devices

Interface Name: importIMEI
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=importIMEI&mds=8b12225ee675411ba2f01a519c7d1d7c&macidsandsims=1990041401,;1990041402,;1990041403,&remark=&objectID=9d6ec2fe-a531-49e9-ac92-8a173c2fe428&product_type=218HY

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| objectID | Guid | Yes | Unit ID |
| product_type | String | Yes | Device Model |
| macidsandsims | String | Yes | Device number string [device number, SIM card; device number, SIM card;] |
| remark | String | No | Service Provider Description |

Development Notes

Three points:

1.  Interface permission verification: Users without device addition permission cannot add devices.
2.  POST request.
3.  The device number string format must use English punctuation.

The method for obtaining the model is attached later.

Request Result



Success:
```json
{
"success": true,
"interance": [], // Failure list
"result": [{ // Success list
"sim_id": "1990041401"
}, {
"sim_id": "1990041402"
}, {
"sim_id": "1990041403"
}],
"num": 3, // Number of successes
"balance": 0, // Price
"errorCode": null, // Error description
"OrderId": null // Order ID
}
```

## Get User Types

Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getUserTypes&mds=c3769e91764d4e10b2a7be75e9e4954d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"Id": "218HY", // The model is this field
"name": "218HY",
"WeightNumber": 0,
"MilNumber": 0,
"OilNumber": 1,
"LatlngNumber": 0,
"TempcNumber": 1,
"BetteryVNumber": 0,
"GpsCountNumber": 0,
"PowerVNumber": 0,
// ... (truncated content)
}]
}
```




"GsmLevelNumber": 0,
"Door": 0,
"Acc": 1,
"Sim": 0,
"Guard": 0,
"OilElec": 1,
"Charger": 0,
"MacType": 17,
"Clockwise": 1,
"Vehicle": 0,
"Lockcnt": 0,
"TimeZone": 0,
"HeightNumber": 0,
"IsOBDDevice": 0,
"Describe": "ok",
"Stroke": 0,
"Photo": 0,
"Cmd": 1,
"Money": 1,
"SignalType": 0,
"MacTypeId": "XY318",
"SaveWifiData": false,
"SaveLbsData": false,
"CanFilter": true,
"CanExplainLBS": 0,
"CanSaveHisState": 0,
"Rental": false,
"Video": 0,
"Pressure": 0
}]
}
```

## 7. Get Device Information (Full)

Interface Name: loadUser
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=loadUser&user_id=e9acf370-9cb7-4d38-9089-97a96f4ad040&mds=8b12225ee675411ba2f01a519c7d1d7c




| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| user_id | Guid | Yes | Device ID |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"user_name": "Emma", // Device name
"sys_time": "2015/09/23 11:45:27", // Latest positioning time
"datetime": "2015/10/26 16:33:48", // Latest position update time
"heart_time": "2015/10/26 16:33:48", // Latest signal time
"sudu": 0.0, // Overspeed setting value
"status": "", // None
"create_time": "2058/04/21", // Platform expiration time
"remark": "", // Remarks
"use_time": "2014/11/29", // Activation time
"factory_date": "2014/11/28", // Factory date
"tel": " ", // Phone
"sex": "Guangdong B96853", // License plate number
"sim_id": "049021604295", // Device number
"phone": "185794666", // SIM card
"user_id": "e9acf370-9cb7-4d38-9089-97a96f4ad040",
"grade": 0, // None
"sale_type": 0, // None
"service_flag": "", // None
"product_type": "XY218C", // Model
"iconType": "01", // Icon number
"out_time": "2058/04/21", // User expiration time
"jingdu": 114.035221666667, // Longitude
"weidu": 22.6485566666667, // Latitude
"su": null, // ICCID
"owner": " ", // Contact person
// ... (truncated content)
}]
}
```



"jingwei": "114.035221666667;22.6485566666667",
"alarm": 0, // Offline alarm switch, 0 off
"deviceRemark": " ", // Service provider description
"alarmFuel": "", // None
"fuelPerHKM": "", // None
"fuelSize": "", // None
"maxFuelV": "", // None
"minFuelV": "", // None
"macVersion": null, // Device software version number
"FuelTotalHeight": "", // None
"carColor": "", // None
"VideoConfig": null, // Video address
"HighTempAlarm": 0.0, // High temperature alarm value
"LowTempAlarm": 0.0, // Low temperature alarm value
"VendorCode": "", // Vendor code
"OwnerId": "", // Owner ID
"PColour": 0, // License plate color
"TerminalType": "", // Terminal type
"DeviceCode": "" // Device code
}]
}
```

## 8.1 [Account Login] Modify Device Information (Full)

Interface Name: modifyUser
Request Method: HTTP
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=modifyUser&mds=77915529595c4b438c9759d23f11490d&info=2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| user_id | Guid | Yes | Device ID |
| info | Int | Yes | Modification type [2 has permission to modify user expiration, 1 no permission] |
| sex | String | No | License plate number |
| user_name | String | No | Device name |
| remark | String | No | Remarks |
| owner | String | No | Contact person |



| tel | String | No | Contact phone |
| phone | String | No | SIM card |
| out_time | String | No | User expiration time |
| alarm | Int | No | Offline alarm switch [0 off 1 on] |
| sudu | Double | No | Overspeed setting value |
| iconType | String | No | Icon number |
| HighTempAlarm | Double | No | High temperature alarm value |
| LowTempAlarm | Double | No | Low temperature alarm value |

Development Notes

None.

Request Result

Success:
```json
{
"success": true
}
```

## 8.2 [Device Login] Modify Device Information (Full)

Interface Name: modifyUser
Request Method: HTTP
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=modifyUser&mds=77915529595c4b438c9759d23f11490d&info=3

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| user_id | Guid | Yes | Device ID |
| info | Int | Yes | Modification type [3] |
| sex | String | No | License plate number |
| user_name | String | No | Device name |
| remark | String | No | Remarks |
| owner | String | No | Contact person |
| tel | String | No | Contact phone |
| phone | String | No | SIM card |



| alarm | Int | No | Offline alarm switch [0 off 1 on] |
| sudu | Double | No | Overspeed setting value |
| iconType | String | No | Icon number |
| HighTempAlarm | Double | No | High temperature alarm value |
| LowTempAlarm | Double | No | Low temperature alarm value |

Development Notes

Comparison with account login modification:

1.  The value of `info` is different, fixed at 3;
2.  The content to be modified is different.

Request Result

Success:
```json
{
"success": true
}
```

## 9. Reset Device Password

Interface Name: resetUserPWD
Request Method: HTTP GET
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=resetUserPWD&user_id=9235223f-4907-4904-bbbc-c92110321541&mds=31532675bc4841ceacf0b1c2ed874946

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| user_id | Guid | Yes | Device ID |

Development Notes

There is permission verification here. If `user_id` is not within the token's management scope, the interface is invalid.




Request Result

Success:
```json
{
"success": true,
"pwd": "123456"
}
```

## 10. Batch Transfer Devices

Interface Name: moveUser
Request Method: HTTP
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=moveUser&cust_id=d7bab2a7-dc43-4546-b9f4-e1bd8d37d774&sim_ids=358740056033425,456456456&mds=77915529595c4b438c9759d23f11490d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| cust_id | Guid | Yes | Device ID |
| sim_ids | String | Yes | Device number string [device number, device number, device number] |

Development Notes

None.

Request Result

Success:
```json
{
"success": true,
"num": 2, // Number of successes
"result": [{ // Success list
"sim_id": "358740056033425"
}, {
"sim_id": "456456456"
}],
"interance": [] // Failure list
}
```




## 11. Batch Delete Devices

Interface Name: batchDeleteProduct
Request Method: HTTP
Request Example: http://apitest.18gps.net/GetDateServices.asmx/GetDate?method=batchDeleteProduct&logn_id=754a5a28-8f24-47ff-908f-c363663ab225&sim_ids=067045065689,358740056031254&custid=e9c939f1-9c06-4312-b295-47f0162b4176&mds=77915529595c4b438c9759d23f11490d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| logn_id | Guid | Yes | Unit ID [Logger] |
| custid | Guid | Yes | Unit ID [User being operated on, can be self] |
| sim_ids | String | Yes | Device number string [device number, device number, device number] |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true"
}
```

## 12. Get Device Album

Interface Name: GetPictureList
Request Method: HTTP GET



Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?&method=GetPictureList&macid=078561000561&Count=15&MinTime=1531535062243&mds=7cace7a6f275496ba5065336c1a16bb2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | Guid | Yes | Device Number |
| MinTime | Long | Yes | Utc timestamp milliseconds (controls retrieval range) |
| Count | Int | Yes | Number of pictures to retrieve |

Development Notes

How to display more?

When retrieving for the first time, set `minTime` to tomorrow. If there are 10 pictures, it means there are more. Design a dropdown or a "more" button. Assign the `minTime` with the earliest `Addtime` from the 10 pictures and call this interface again to get more. Repeat this process to retrieve all pictures.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"Id": "08ded06e-6b4c-4b3f-a8fd-b94ac36283f0",
"Macid": "078561000561",
"PicName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&fileName=078561000561_201802020855539804.jpg&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:55:55",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561755295
}, {
"Id": "305bf138-05e1-4a42-a2e6-75fcdb473cb6",
"Macid": "078561000561",
"PicName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&fileName=078561000561_201802020855539804.jpg&mds=7cace7a6f275496ba5065336c1a16bb2",
// ... (truncated content)
}]
}
```



method=DownLoadPicOrVideo&fileName=078561000561_201802020855336913.jpg&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:55:35",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561735001
}, {
"Id": "fc64de3d-7a48-4033-9874-b9ba409585e5",
"Macid": "078561000561",
"PicName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&fileName=078561000561_201802020855096289.jpg&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:55:11",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561711460
}, {
"Id": "94219176-fd25-4a4b-b686-56f5406ed75c",
"Macid": "078561000561",
"PicName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&fileName=078561000561_201802020854248323.jpg&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:54:27",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561667196
}]
}
```

## 13. Get Device Video

Interface Name: GetVideoList
Request Method: HTTP GET
Request Example: http://apitest.18gps.net//GetDateServices.asmx/GetDate?&method=GetVideoList&macid=078561000561&Count=10&MinTime=1531535094212mds=7cace7a6f275496ba5065336c1a16bb2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |



| mds | String | Yes | Token |
| macid | Guid | Yes | Device Number |
| MinTime | Long | Yes | Utc timestamp milliseconds (controls retrieval range) |
| Count | Int | Yes | Number of pictures to retrieve |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"Id": "413ac1cc-5796-4c53-9bf1-1574049c602c",
"Macid": "078561000561",
"VideoName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&video=078561000561_201802020856222301.mp4&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:56:25",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561785131
}, {
"Id": "4266c302-f49b-47a8-881b-3a1c616201fc",
"Macid": "078561000561",
"VideoName": "http://vipapi.18gps.net/GetDateServices.asmx/GetDate?method=DownLoadPicOrVideo&video=078561000561_201802020854457805.mp4&mds=7cace7a6f275496ba5065336c1a16bb2",
"Addtime": "2018/02/02 16:54:48",
"Lat": 22.701765,
"Lon": 113.821802,
"Time": 1517561688710
}]
}
```




# WebApi Web Version Interface

This is the web version interface. Provide parameters to get the web page.

http://www.18gps.net/GetDataService.aspx?method=loginSystem&LoginName=nunu&LoginPassword=123456&LoginType=ENTERPRISE&language=cn&ISMD5=0&timeZone=8&apply=APP&loginUrl=

## Follow Page

Interface Name: Follow
Request Method: HTTP GET
Request Example:
http://www.18gps.net:8028/RouterPass.ashx?method=Follow&mds=2ae501b9a238468b9715e8c5a90fc476&custid=1a9b8748-ff05-450a-9edd-9dfebbf0fbae&mapType=BAIDU&deviceid=7a01aa2e-0df2-43d7-8b03-2bea882f336b&deviceName=Guo%20sir

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID |
| mapType | String | Yes | Map Type (BAIDU) |



| deviceid | Guid | Yes | Device ID |
| deviceName | String | No | Device Name |

Interface Description: None.

## Playback Page

Interface Name: Playback
Request Method: HTTP GET
Request Example:
http://www.18gps.net:8028/RouterPass.ashx?method=Playback&mds=2ae501b9a238468b9715e8c5a90fc476&custid=1a9b8748-ff05-450a-9edd-9dfebbf0fbae&mapType=BAIDU&deviceid=7a01aa2e-0df2-43d7-8b03-2bea882f336b&deviceName=Guo%20sir

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| custid | Guid | Yes | Unit ID |
| mapType | String | Yes | Map Type (BAIDU) |
| deviceid | Guid | Yes | Device ID |



| deviceName | String | No | Device Name |

Interface Description: None.

# OBD Box

OBD is the abbreviation for On-Board Diagnostics, which translates to "On-Board Diagnostic System" in Chinese. It looks like the image below, and its installation position is above the left side of the driver's brake pedal, as shown in the second image. This device is famous for its last fault code, vehicle condition data, and trip data.

## NO.1 Get Fault Code Data [2018]

Interface Name: getCurCodeV3
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=getCurCodeV3&macid=601602230004&mds=cb445c3a41d940f69aa5c636d0cf2274

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |



| mds | String | Yes | Token |
| macid | String | Yes | Device Number |

Development Notes

When `data` is an empty array "[]", it means no fault codes were detected.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"err": "",
"info": "",
"data": [
{
"sysId": "2", // Meaningless
"obdcode": "P0205", // Fault code name
"obdcodestr": "P0205", // Fault code name
"obddescription": "Injector Circuit/Open - Cylinder 5", // Fault code translation
"obddescription_en": ""
},
{
"sysId": "0",
"obdcode": "P0308",
"obdcodestr": "P0308",
"obddescription": "Misfire Detected Cylinder 8",
"obddescription_en": ""
}
],
"obdSys": [
{
"sysId": "0", // Meaningless
"obdTitle": "Ignition System", // Which system it belongs to (16 system titles at the bottom)
"obdCount": 1 // Number of fault codes in this system
},
{
"sysId": "1",
"obdTitle": "Body Computer Control System",
// ... (truncated content)
}]
}
```



"obdCount": 0
},
{
"sysId": "2",
"obdTitle": "Fuel and Air Detection System",
"obdCount": 1
},
{
"sysId": "3",
"obdTitle": "Exhaust Control System",
"obdCount": 0
},
{
"sysId": "4",
"obdTitle": "Transmission Control System",
"obdCount": 0
},
{
"sysId": "5",
"obdTitle": "Fuel and Air Detection System",
"obdCount": 0
},
{
"sysId": "6",
"obdTitle": "Vehicle Speed Idle Control System",
"obdCount": 0
},
{
"sysId": "7",
"obdTitle": "Computer Control System",
"obdCount": 0
},
{
"sysId": "8",
"obdTitle": "Network System",
"obdCount": 0
},
{
"sysId": "9",
"obdTitle": "Chassis Computer Control System",
"obdCount": 0
},
// ... (truncated content)
]
}
```



"sysId": "10",
"obdTitle": "Other Systems",
"obdCount": 0
}
],
"fraction": 80
}
```

Fault code systems are divided into 16 categories:

*   Body System
*   Chassis System
*   Fuel Air or Emission Control
*   Fuel or Air
*   Ignition System
*   Emission Control
*   Vehicle Speed and Idle Control
*   Computer or Auxiliary Output Circuit
*   Transmission
*   Hybrid System
*   Powertrain System
*   Auxiliary Input
*   Computer or Auxiliary Output
*   Cylinder Deactivation
*   Network Communication System
*   Other Systems

## NO.2 Get Latest Vehicle Condition Data [2018]

Interface Name: OBDLatestCarCondition
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=OBDLatestCarCondition&macid=601804250020&mds=c8837ff880e4453ca2b168041b64e46d

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |



| mds | String | Yes | Token |
| macid | String | Yes | Device Number |

Development Notes

None.

Request Result

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"Number": "P_01",
"Key": "Vehicle Speed",
"Value": "0 km/h"
}, {
"Number": "P_02",
"Key": "Engine Speed",
"Value": "601 rpm"
}, {
"Number": "P_03",
"Key": "Engine Coolant Temperature",
"Value": "74 ℃"
}, {
"Number": "P_10",
"Key": "Calculated Engine Load Value",
"Value": "16 %"
}, {
"Number": "P_13",
"Key": "Instantaneous Fuel Consumption",
"Value": "100 L/100km"
}, {
"Number": "P_18",
"Key": "Control Module Voltage",
"Value": "27 V"
}, {
"Number": "P_19",
"Key": "Absolute Throttle Position D",
// ... (truncated content)
}]
}
```



"Value": "0 %"
}, {
"Number": "P_21",
"Key": "Brake Status",
"Value": "Brake not engaged"
}, {
"Number": "P_23",
"Key": "Instantaneous Fuel Consumption",
"Value": "2 L/h"
}, {
"Number": "P_26",
"Key": "OBD Module Status",
"Value": "In trip"
}]
}
```

## NO.3 Get Last Trip [2018] (Single Trip)

Interface Name: getStrokeData
Request Method: HTTP GET
Request Example: http://api.18gps.net//GetDateServices.asmx/GetDate?method=getStrokeData&mds=befcda89ec02489f9f6f76695a94cb3d&user_id=20992b1f-315b-4920-b9a4-d3509f33a3c2

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Function Call |
| mds | String | Yes | Token |
| user_id | Guid | Yes | Device ID |




Development Notes

None.

Request Result

Success:
```json
{
"id": "bd1382e2-af21-44b6-ba00-c2c62e49c3f3",
"btime": 1533254331000,
"etime": 1533263282000,
"distance": 46.42,
"maxspeed": 62.0,
"totalSpeedoverSeconds": null,
"brakeTimes": 0,
"emergencyBrakeTimes": 0,
"speedupTimes": 0,
"emergencySpeedupTimes": 0,
"speed": 18,
"maxTempc": 0.0,
"maxEngRPM": 1977,
"powerv": 0.0,
"totalfuelUsed": 18.737,
"fuelHKM": 0.0,
"overtimeDriverMinutes": 0,
"extend": [{
"Number": "T_01",
"Key": "Driving Time",
"Value": "4934 s"
}, {
"Number": "T_03",
"Key": "Trip Time",
"Value": "8951 s"
}, {
"Number": "T_08",
"Key": "ECU Total Mileage",
"Value": "4463300 km"
}, {
"Number": "T_15",
"Key": "Average Speed",
"Value": "88300 rpm"
}, {
"Number": "T_16",
"Key": "Average Throttle",
"Value": "1200 %"
}, {
"Number": "T_17",
// ... (truncated content)
}]
}
```



"Key": "Average Load",
"Value": "2500 %"
}, {
"Number": "T_21",
"Key": "Brake Count",
"Value": "202 times"
}]
}
```

## Get Vehicle Condition Data [Obsolete]

Interface Name: getCarHardWare/getVehicleData
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?&method=getCarHardWare&macid=xy1610100063&mds=27643b87176b454492f45305924ef391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number |
| mds | String | Yes | Token |

Interface Description: None.


Request Result:

Success:



```json
{
"success": "true",
"errorCode": 200,
"errorDescribe": "Successfully retrieved vehicle condition data",
"obdList": [],
"obdcode": "Health Score",
"obdcodestr": "Health Score",
"obddescription": "100",
"rows": {
"macid": "xy1610100063",// Device number
"P1": 0.0,// Vehicle speed
"P2": 0.0,// Engine speed
"P3": 0.0,// Engine coolant temperature
"P4": 65.535,// Air flow
"P5": 0.0,// Air temperature
"P6": 0.0,// Intake manifold absolute pressure
"P7": 0.0,// Long term fuel trim
"P8": 0.0,// Ambient temperature
"P9": 0.0,// Mileage
"P10": 0.0,// Calculated load value
"P11": 0.0,// Absolute throttle position
"P12": 0.0,// Cylinder 1 ignition advance angle
"P13": 0.0,// Instantaneous fuel consumption
"P14": 0.0,// Accumulated fuel consumption
"P15": 0.0,// Accelerator pedal D
"P16": 0.0,// Accelerator pedal E
"P17": 0.0,// Accelerator pedal F
"P18": 0.0,// Control module voltage
"P47": 0.0,// Remaining fuel
"speed": 0.0,// Speed
"fraction": "100",// Score
"StateTime": "2018/1/25 17:12:50",// Vehicle condition status time
"CurCodeTime": "2018/1/25 17:12:13",// Latest fault code upload time
"HisCodeTime": "2018/1/25 17:12:13"// This field [deprecated]
}
}
```
Failure:
```json
{}
```




## Get Fault Code [Obsolete]

Interface Name: getCurCodeV3
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getCurCodeV3&macid=xy1610100063&mds=27643b87176b454492f45305924ef391

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| macid | String | Yes | Device Number |
| mds | String | Yes | Token |

Interface Description: None.

Request Result:

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"err": "",
"info": "",
"data": [],// Fault codes
"obdSys": [{
"sysId": "0",
"obdTitle": "Body System",// Description
"obdCount": 0
}, {
"sysId": "1",
"obdTitle": "Chassis System",
"obdCount": 0
}],
"fraction": 100
}
```
Failure:
```json
{}
```

## NO.4 Get Trip

Interface Name: getStrokeV3/getStrokeV3_extend
Request Method: HTTP GET
Request Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=getStrokeV3&macid=xy1610100063&beginTime=1517129108109&endTime=1517129208109

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macid | String | Yes | Device Number |



| beginTime | Long | Yes | Utc Timestamp (start time) |
| endTime | Long | Yes | Utc Timestamp (end time) |

Interface Description:

Request Result:

Success:
```json
{
"success": "true",
"errorCode": "200",
"errorDescribe": "",
"data": [{
"rows": [{
"totalfuelUsed": "0.5", // Total fuel consumption
"distance": "3.3", // Driving distance
"fuelHKM": "15.2",// Fuel consumption per hundred kilometers
"totalSpeedoverSeconds": null,// Overspeed driving time
"maxspeed": "61",// Maximum speed
"brakeTimes": 0,// Stop time
"emergencyBrakeTimes": 0,// Emergency deceleration times
"speedupTimes": 0,// Rapid acceleration duration
"emergencySpeedupTimes": 0,// Rapid acceleration times
"speed": "0",// Average speed
"maxTempc": 86,// Maximum water temperature
"btime": "7:21",// Start time
"etime": "7:34",// End time
"strokeTime": "2018/12/13",// Date
"jsType": 2,//
"driveTime": 780.0,// Driving time (seconds)
"fraction": 100.0,//
"random": "95%",//
"coulometric": 0,//
"ExtendedData": {// Extended data
"SpeedLimitFuel": 0,
"SpeedLimitMil": 0,
"SpeedLimitTime": 0,
// ... (truncated content)
}]
}
]
}
```



"HighSpeedFuel": 0,
"HighSpeedMil": 0,
"HighSpeedTime": 0,
"MediumSpeedFuel": 0,
"MediumSpeedMil": 0,
"MediumSpeedTime": 0,
"LowSpeedFuel": 0,
"LowSpeedMil": 0,
"LowSpeedTime": 0,
"IdleSpeedFuel": 0,
"IdleSpeedTime": 0,
"SharpTurnCount": 0,
"SpeedLimitCount": 0,
"FastTrackCount": 0
},
"BeginTime": 1544656903000,//utc
"EndTime": 1544657683000//utc
}],
"avgFuel": "12.45",// Average fuel consumption for this query
"totalfuel": "2.9",// Total fuel consumption for this query
"totalmil": "23.3",// Total mileage for this query
"totalDriveTime": "6198",// Total driving time for this query (seconds)
"totalCoulometric": "0",//..
"oilPrice": 0.0//..
}]
}
```
Failure:
```json
{}
```

## NO.5 Batch Get Trips (Supports Single) D Platform Support

Interface Name: BatchGetTrips
Request Method: HTTP GET
Request Format:



Example:
http://apitest.18gps.net//GetDateServices.asmx/GetDate?method=BatchGetTrips&macids=xy1610100063,601603080472&beginTime=1519780593918&endTime=1519803593918&mds=268cf32b992e4504b8d4f692446afe82

| Parameter | Type | Required | Description |
|---|---|---|---|
| method | String | Yes | Interface Name |
| mds | String | Yes | Token |
| macids | String[] | Yes | Device List |
| beginTime | Long | Yes | Utc Timestamp (start time) |
| endTime | Long | Yes | Utc Timestamp (end time) |

Interface Description:

Request Result:
```json
{
    "totalfuelUsed": "0.3", // Total fuel consumption
    "distance": "3.0", // Distance (km)
    // ... (truncated content)
    "fuelHKM": "11.7", // Fuel consumption per hundred kilometers
    "totalSpeedoverSeconds": null, // Overspeed driving time
    "maxspeed": "50", // Maximum speed
    "brakeTimes": 0, // Stop time (unit: seconds)
    "emergencyBrakeTimes": 1, // Emergency deceleration times
    "speedupTimes": 0, // Rapid acceleration time (unit: seconds)
    "emergencySpeedupTimes": 0, // Rapid acceleration times
    "speed": "14", // Average speed
    "maxTempc": 68, // Maximum water temperature
    "maxEngRPM": 1500, // Maximum engine speed
    "powerv": 14.2, // Voltage
    "overtimeDriverMinutes": 1500, // Fatigued driving (seconds)
    "btime": "10:49:25", // Start time
    "etime": "11:02:23", // End time
    "strokeTime": "2018/2/28", // Date
    "jsType": 0, // 0: Vanguard 1: Standard 2: Steady 3: Slowpoke
    "driveTime": 745.0, // Total driving seconds
    "fraction": 0.0,
    "random": null,
    "coulometric": 0
},
```

Success:
```json
{}
```



Failure:
```json
{}
```


