# GPS HTTP Interface Documentation
This repo contains detailed documentation for **5 core GPS HTTP interfaces** designed for device tracking, trajectory playback, mileage statistics, centralized monitoring, and alarm management. It’s tailored for developers integrating GPS positioning functionalities into applications, with clear parameter definitions, response formats, and error code explanations.

### Key Interfaces Covered
1.  **Real-Time Tracking**: Fetch live device location (latitude/longitude, speed, status).
2.  **Historical Track Playback**: Retrieve past device trajectories (supports time-range filtering, max 1000 records).
3.  **Mileage Report**: Generate daily mileage statistics (35-day max time range).
4.  **Centralized Monitoring**: Query all devices under a user account (max 300 devices per request).
5.  **Alarm Message**: Get paginated device alarm records (e.g., disconnection alerts).

### Core Details
*   **Authentication**: Uses a fixed unique KEY (`OUCHUANG20170602A009`) for API access.
*   **Map Compatibility**: Supports Baidu/Google Maps (coordinate correction) or raw uncorrected coordinates.
*   **Error Handling**: Clear state codes (e.g., `2003` for time-range excess, `3001` for invalid KEY) for debugging.

### Usage Note
Avoid malicious/millisecond-frequency calls to prevent IP blocking. All request/response formats follow JSON standards for easy parsing.

## 1. Interface Call Precautions
*   Do not call the interface maliciously or at a millisecond-level frequency. Any violation will result in direct IP blocking.
*   Unique KEY: `OUCHUANG20170602A009`
## 2. Real-Time Tracking Interface
### 2.1 Request URL

```
http://www.zyhgps.com/api/Tracking.aspx?sn=&mapType=google&key=OUCHUANG20170602A009
```

### 2.2 Request Parameters

| Parameter | Description                                                                                                                                             | Required |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| sn        | Unique device ID                                                                                                                                        | Yes      |
| mapType   | Longitude and latitude map type. Options: `baidu` (Baidu Map), `google` (Google Map), or empty. Empty means original uncorrected longitude and latitude | No       |
| key       | Unique KEY (fixed as `OUCHUANG20170602A009`)                                                                                                            | Yes      |

### 2.3 Successful Response Data
```
{
&#x20; "state": "0",
&#x20; "positionTime": "2017-06-03 10:46:10",
&#x20; "lat": "31.26359",
&#x20; "lng": "121.60340",
&#x20; "speed": "0.00",
&#x20; "course": "150",
&#x20; "isStop": "1",
&#x20; "isGPS": "1",
&#x20; "stm": "203",
&#x20; "status": "2-Battery Level 100%"
}
```

### 2.4 Response Parameter Explanation



| Parameter    | Description                                                                                                                                                                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| state        | Request status. `0` means success (see Section 7 for other status codes)                                                                                                                                                                                     |
| positionTime | Positioning time                                                                                                                                                                                                                                             |
| lat          | Latitude                                                                                                                                                                                                                                                     |
| lng          | Longitude                                                                                                                                                                                                                                                    |
| speed        | Speed                                                                                                                                                                                                                                                        |
| course       | Angle                                                                                                                                                                                                                                                        |
| isStop       | Whether the device is stopped. `1` = stopped, `0` = moving                                                                                                                                                                                                   |
| isGPS        | Positioning method. `0` = LBS, `1` = GPS, `2` = WIFI                                                                                                                                                                                                         |
| stm          | Dwell time                                                                                                                                                                                                                                                   |
| status       | Current device online/offline status - vehicle status. Status code meaning: `0` = unused, `1` = moving, `2` = stationary, `3` = offline, `4` = arrears. Separator: `-`, the content after the separator is the vehicle status (display directly in the list) |

## 3. Historical Track Interface
### 3.1 Request URL
```
http://www.zyhgps.com/api/Playback.aspx?sn=&mapType=google&startTime=2017-06-03 3:00&endTime=2017-06-03 10:00&showLBS=1&key=OUCHUANG20170602A009
```
### 3.2 Request Parameters
| Parameter | Description                                                                                                                 | Required |
| --------- | --------------------------------------------------------------------------------------------------------------------------- | -------- |
| sn        | Unique device ID                                                                                                            | Yes      |
| mapType   | Longitude and latitude map type. Options: `baidu`, `google`, or empty (empty = original uncorrected longitude and latitude) | No       |
| startTime | Start time of track search (format: `YYYY-MM-DD HH:MM`)                                                                     | Yes      |
| endTime   | End time of track search (format: `YYYY-MM-DD HH:MM`)                                                                       | Yes      |
| showLBS   | Whether to search LBS base station positioning data. `0` = no, `1` = yes                                                    | Yes      |
| key       | Unique KEY (`OUCHUANG20170602A009`)                                                                                         | Yes      |

### 3.3 Important Notes
*   A maximum of 1000 pieces of data will be returned. It is recommended to search historical tracks by day or by hour.

### 3.4 Successful Response Data
```
{
&#x20; "state": "0",
&#x20; "devices": \[
&#x20;   {
&#x20;     "pt": "2017-06-03 07:09:47",
&#x20;     "lat": "31.29670",
&#x20;     "lng": "121.62576",
&#x20;     "s": "11.00",
&#x20;     "c": "299",
&#x20;     "stop": "0",
&#x20;     "stm": "0",
&#x20;     "id": "8038580",
&#x20;     "g": "1"
&#x20;   },
&#x20;   {
&#x20;     "pt": "2017-06-03 07:09:55",
&#x20;     "lat": "31.29682",
&#x20;     "lng": "121.62561",
&#x20;     "s": "13.00",
&#x20;     "c": "357",
&#x20;     "stop": "0",
&#x20;     "stm": "0",
&#x20;     "id": "8039193",
&#x20;     "g": "1"
&#x20;   }
&#x20; ]
}
```

### 3.5 Response Parameter Explanation
| Parameter         | Description                                                               |
| ----------------- | ------------------------------------------------------------------------- |
| state             | Request status. `0` = success                                             |
| devices           | Array of track data (each element represents a single positioning record) |
| pt (in devices)   | Device positioning time                                                   |
| lat (in devices)  | Latitude                                                                  |
| lng (in devices)  | Longitude                                                                 |
| s (in devices)    | Speed                                                                     |
| c (in devices)    | Angle                                                                     |
| stop (in devices) | Whether stopped. `1` = stopped, `0` = moving                              |
| stm (in devices)  | Stopping minutes                                                          |
| id (in devices)   | Unique ID for each record                                                 |
| g (in devices)    | Positioning method. `0` = LBS, `1` = GPS, `2` = WIFI                      |

## 4. Mileage Report Interface

### 4.1 Request URL
```
http://www.zyhgps.com/api/GetDistanceReport.aspx?sn=4209527377&startTime=2017-11-01 00:00&endtime=2017-11-28 00:00&key=OUCHUANG20170602A009
```

### 4.2 Request Parameters
| Parameter | Description                                       | Required |
| --------- | ------------------------------------------------- | -------- |
| sn        | Unique device ID                                  | Yes      |
| startTime | Start time of search (format: `YYYY-MM-DD HH:MM`) | Yes      |
| endTime   | End time of search (format: `YYYY-MM-DD HH:MM`)   | Yes      |
| key       | Unique KEY (`OUCHUANG20170602A009`)               | Yes      |

### 4.3 Important Notes
*   The search time range cannot exceed 35 days; otherwise, error code `2003` will be returned.

### 4.4 Successful Response Data
```
{
&#x20; "reports": \[
&#x20;   {
&#x20;     "id": "101767071",
&#x20;     "distance": "13.45",
&#x20;     "date": "2017/11/01"
&#x20;   },
&#x20;   {
&#x20;     "id": "102066975",
&#x20;     "distance": "0.95",
&#x20;     "date": "2017/11/02"
&#x20;   }
&#x20; ]
}
```

### 4.5 Response Parameter Explanation
| Parameter             | Description                                |
| --------------------- | ------------------------------------------ |
| reports               | Array of mileage data (one record per day) |
| id (in reports)       | Data ID                                    |
| distance (in reports) | Mileage (unit: kilometer)                  |
| date (in reports)     | Date (one record per day)                  |

## 5. Monitoring Center Interface
### 5.1 Function Description
Query the positions of all devices under a user based on the username.

### 5.2 Important Notes
*   A maximum of 300 device positions can be queried at one time. For more than 300 devices, multiple accounts are required.
### 5.3 Request URL
```
http://www.zyhgps.com/api/Monitor.aspx?loginname=&password=&mapType=google&key=OUCHUANG20170602A009
```

### 5.4 Request Parameters
| Parameter | Description                                                                                                                 | Required |
| --------- | --------------------------------------------------------------------------------------------------------------------------- | -------- |
| loginname | Login account                                                                                                               | Yes      |
| password  | Login password                                                                                                              | Yes      |
| mapType   | Longitude and latitude map type. Options: `baidu`, `google`, or empty (empty = original uncorrected longitude and latitude) | No       |
| key       | Unique KEY (`OUCHUANG20170602A009`)                                                                                         | Yes      |

### 5.5 Successful Response Data

```
{
&#x20; "devices": \[
&#x20;   {
&#x20;     "id": "87762",
&#x20;     "name": "M1-91732",
&#x20;     "sn": "4209091732",
&#x20;     "modelName": "M1",
&#x20;     "carNum": "",
&#x20;     "deviceUtcDate": "2017-11-23 20:50:10",
&#x20;     "baiduLat": "24.38061",
&#x20;     "baiduLng": "117.32910",
&#x20;     "speed": "0.00",
&#x20;     "course": "998",
&#x20;     "isGPS": "2",
&#x20;     "distance": "13172.9277",
&#x20;     "isStop": "1",
&#x20;     "stopTimeMinute": "2172",
&#x20;     "status": "3-Battery Level 80%"
&#x20;   }
&#x20; ]
}
```

### 5.6 Response Parameter Explanation
| Parameter                   | Description                                                                                                                                                                                                                         |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| devices                     | Array of device data                                                                                                                                                                                                                |
| id (in devices)             | Data ID                                                                                                                                                                                                                             |
| name (in devices)           | Device name                                                                                                                                                                                                                         |
| sn (in devices)             | Device IMEI number                                                                                                                                                                                                                  |
| modelName (in devices)      | Device model                                                                                                                                                                                                                        |
| carNum (in devices)         | Vehicle license plate number                                                                                                                                                                                                        |
| deviceUtcDate (in devices)  | Device positioning time                                                                                                                                                                                                             |
| baiduLat (in devices)       | Baidu Map latitude                                                                                                                                                                                                                  |
| baiduLng (in devices)       | Baidu Map longitude                                                                                                                                                                                                                 |
| speed (in devices)          | Speed                                                                                                                                                                                                                               |
| course (in devices)         | Direction                                                                                                                                                                                                                           |
| isGPS (in devices)          | Positioning method. `0` = LBS, `1` = GPS, `2` = WIFI                                                                                                                                                                                |
| distance (in devices)       | Total mileage (unit: kilometer)                                                                                                                                                                                                     |
| isStop (in devices)         | Whether stationary. `1` = stationary, `0` = moving                                                                                                                                                                                  |
| stopTimeMinute (in devices) | Stationary time (unit: minute)                                                                                                                                                                                                      |
| status (in devices)         | Current device online/offline status - vehicle status. Status code: `0` = unused, `1` = moving, `2` = stationary, `3` = offline, `4` = arrears. Separator: `-`, content after separator = vehicle status (display directly in list) |

## 6. Alarm Message Interface
### 6.1 Request URL
```
http://www.zyhgps.com/api/WarnListByAPI.aspx?imei=&password=&mapType=baidu&PageNo=1&PageCount=2&key=OUCHUANG20170602A009
```
### 6.2 Request Parameters
| Parameter | Description                                                                                                                 | Required |
| --------- | --------------------------------------------------------------------------------------------------------------------------- | -------- |
| imei      | Device number                                                                                                               | Yes      |
| password  | Device login password                                                                                                       | Yes      |
| mapType   | Longitude and latitude map type. Options: `baidu`, `google`, or empty (empty = original uncorrected longitude and latitude) | No       |
| PageNo    | Page number (starts from 1)                                                                                                 | Yes      |
| PageCount | Number of records per page                                                                                                  | Yes      |
| key       | Unique KEY (`OUCHUANG20170602A009`)                                                                                         | Yes      |

### 6.3 Successful Response Data

```
{
&#x20; "state": "0",
&#x20; "nowPage": "1",
&#x20; "resSize": "1",
&#x20; "arr": \[
&#x20;   {
&#x20;     "id": "393860749",
&#x20;     "name": "M2-05253",
&#x20;     "model": "M2",
&#x20;     "warn": "Disconnection Alarm",
&#x20;     "deviceDate": "2020/03/04 14:37",
&#x20;     "createDate": "2020/03/04 14:37"
&#x20;   }
&#x20; ]
}
```

### 6.4 Response Parameter Explanation
| Parameter           | Description                   |
| ------------------- | ----------------------------- |
| state               | Request status. `0` = success |
| nowPage             | Current page number           |
| resSize             | Total number of records       |
| arr                 | Array of alarm data           |
| id (in arr)         | Data record ID                |
| name (in arr)       | Device name                   |
| model (in arr)      | Device model                  |
| warn (in arr)       | Alarm content                 |
| deviceDate (in arr) | Positioning time              |
| createDate (in arr) | Alarm time                    |

## 7. State Code Explanation
| State Code | Description                                                           |
| ---------- | --------------------------------------------------------------------- |
| 0          | Success                                                               |
| 1001       | Incorrect parameters                                                  |
| 1002       | Program error/exception (may be caused by incorrect parameters, etc.) |
| 2002       | No results found                                                      |
| 2003       | Search time range exceeded                                            |
| 2004       | Incorrect account or password                                         |
| 3001       | Incorrect KEY                                                         |
