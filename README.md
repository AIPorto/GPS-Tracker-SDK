# GPS Tracker SDK

This repository provides a comprehensive Software Development Kit (SDK) for integrating GPS tracking functionalities into your applications. It includes detailed documentation for the core GPS HTTP interfaces, device protocols, and API usage guidelines.

This SDK is designed for developers looking to implement robust and scalable GPS tracking solutions for device monitoring, trajectory analysis, and alarm management.

## Features

The GPS Tracker SDK is equipped with a robust set of features designed to provide a comprehensive solution for location tracking and management. It enables developers to build sophisticated applications for a variety of use cases, from simple device tracking to complex fleet management.

| Feature                        | Description                                                                                             |
| ------------------------------ | ------------------------------------------------------------------------------------------------------- |
| **Real-time Tracking**         | Monitor the live location of GPS devices with high precision and low latency.                           |
| **Historical Playback**        | Access and review detailed historical routes and trajectories of any device.                            |
| **Mileage Statistics**         | Automatically calculate and generate reports on the distance traveled by individual or groups of devices. |
| **Centralized Monitoring**     | A unified dashboard allows for the management and monitoring of a large number of devices simultaneously.   |
| **Advanced Alarm System**      | A flexible alarm system supports a wide range of events, including SOS, over-speed, and geo-fence alerts. |
| **Geofencing**                 | Create, manage, and monitor virtual boundaries to trigger alerts upon device entry or exit.             |
| **Cross-Platform Integration** | Designed for easy integration with Web applications, native mobile apps (iOS/Android), and WeChat Mini Programs. |

## Getting Started

To begin integrating the GPS Tracker SDK, follow these steps:

1.  **Explore the Documentation**: Familiarize yourself with the API and device protocols provided in the `docs` folder.
2.  **Obtain Credentials**: Use the `loginSystem` interface to get your authentication token (`mds`) and user ID.
3.  **Integrate the APIs**: Use the provided API endpoints to build your application features.



## API Reference

The core of the SDK is the GPS HTTP API, which provides a set of powerful endpoints to interact with your devices. For complete details, please refer to the [GPS Tracker Open API Documentation](GPS_Trcker_Open_API.md).

### Key API Functionalities

- **Authentication**: Secure your API access with token-based authentication.
- **Device Management**: Programmatically manage your fleet of devices.
- **Location and Tracking**: Access real-time and historical location data.
- **Alarm and Notification**: Receive and manage device alerts.
- **Geofencing**: Create and monitor virtual boundaries.


## Device Protocols

This SDK supports multiple device communication protocols. Detailed documentation for each protocol is available in the repository:

- **G10LS Protocol**: [G10LS_Protocol.md](G10LS_Protocol.md)
- **G20LS Protocol**: [G20LS_Protocol.md](G20LS_Protocol.md)

These documents provide the necessary information to configure and communicate with the respective GPS devices.



## Usage

The GPS Tracker SDK can be used for a wide range of applications, including:

- **Fleet Management**: Track and manage your fleet of vehicles in real-time.
- **Asset Tracking**: Monitor the location and status of your valuable assets.
- **Personal Safety**: Provide location tracking and SOS features for personal safety applications.
- **Logistics and Delivery**: Optimize routes and monitor the progress of your delivery fleet.


