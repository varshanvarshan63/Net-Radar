# NetRadar — Mobile Network Monitoring & Authorized Device Tracking

> A full-stack mobile network monitoring and authorized device-location platform designed to collect, store, analyze, and visualize network and GPS information from devices that have explicitly granted the required permissions.

---

## 1. Project Overview

**NetRadar** is a mobile network monitoring and authorized device tracking system.

The project combines:

* Device registration
* GPS location collection
* Network information monitoring
* Cell/network metadata
* Signal-strength monitoring
* Device status monitoring
* Historical location records
* Database storage
* Web-based dashboard
* REST APIs
* Interactive map visualization
* Network statistics
* Device management

The primary goal of NetRadar is to provide a centralized dashboard where authorized users can monitor devices and visualize the network information and location data those devices voluntarily provide.

### Important limitation

NetRadar **does not obtain the secret or live telecom location of a phone simply from its mobile number**.

A phone number alone does not provide a normal application with:

* Exact GPS coordinates
* SIM subscriber location
* Private carrier database information
* IMSI
* Private telecom network information
* Cell-tower triangulation results
* Another person's real-time location

Such information requires appropriate carrier/operator infrastructure, lawful authorization, or specialized telecom systems.

NetRadar instead works with information supplied by an authorized device/application.

---

# 2. Main Objectives

The main objectives of NetRadar are:

1. Monitor authorized mobile devices.
2. Collect GPS coordinates from devices with permission.
3. Record network-related measurements.
4. Store historical measurements in a database.
5. Display device locations on an interactive map.
6. Monitor signal strength and network status.
7. Track device connectivity status.
8. Provide APIs for mobile/web clients.
9. Provide a centralized monitoring dashboard.
10. Analyze historical network and location data.

---

# 3. Key Features

## 3.1 Device Registration

Administrators can register authorized devices.

Example information:

```text
Device Name
Device ID
User/Owner
Phone Number
Operating System
Device Model
Registration Date
Status
```

Example:

```text
Device Name: Varshan Phone
Device ID: DEV-001
OS: Android
Model: Samsung Galaxy
Status: Online
```

---

# 3.2 GPS Location Tracking

If the authorized device grants location permission, the application can periodically send:

```text
Latitude
Longitude
Accuracy
Altitude
Speed
Heading
Timestamp
```

Example:

```json
{
    "latitude": 12.9716,
    "longitude": 77.5946,
    "accuracy": 8.5,
    "altitude": 920,
    "speed": 12.4,
    "heading": 180,
    "timestamp": "2026-08-17T13:30:00"
}
```

The server stores these measurements for later visualization.

---

# 3.3 Network Information

Depending on the operating system and permissions available to the application, NetRadar can collect available network information such as:

```text
Network Type
Operator
MCC
MNC
Cell ID
Signal Strength
Connection State
Wi-Fi Information
IP Information
```

Possible network types include:

```text
2G
3G
4G
5G
Wi-Fi
Unknown
```

Availability of particular cellular fields depends on the device, Android/iOS version, permissions, carrier, and application capabilities.

---

# 3.4 Signal Strength Monitoring

NetRadar can record signal measurements when the authorized device exposes them.

Example:

```text
Signal Strength: -72 dBm
Network: 4G LTE
Quality: Good
```

Typical interpretation:

| Signal   | Approximate Meaning |
| -------- | ------------------- |
| -50 dBm  | Excellent           |
| -60 dBm  | Very Good           |
| -70 dBm  | Good                |
| -80 dBm  | Fair                |
| -90 dBm  | Weak                |
| -100 dBm | Very Weak           |
| -110 dBm | Extremely Weak      |

These values are approximate and vary according to radio technology and measurement method.

---

# 3.5 Cell Information

Where the authorized device exposes cellular information, NetRadar can store values such as:

```text
MCC
MNC
Cell ID
LAC/TAC
Network Type
Signal Strength
Timestamp
```

Example:

```text
MCC: 404
MNC: 45
Cell ID: 12345678
TAC: 5021
Network: LTE
Signal: -76 dBm
```

### Important

A Cell ID is **not automatically an exact GPS coordinate**.

Cell information identifies a cellular network area/base-station relationship. Converting cell information into an estimated geographical position requires appropriate cell databases or operator data.

---

# 3.6 Device Online/Offline Status

NetRadar can determine whether a registered client has recently communicated with the server.

Example:

```text
Online
Last Seen: 20 seconds ago
```

or:

```text
Offline
Last Seen: 12 minutes ago
```

The server can use a heartbeat mechanism.

Example:

```text
Device → Server
       ↓
Heartbeat
       ↓
Server updates last_seen
```

---

# 3.7 Interactive Map

The dashboard can display authorized device locations on a map.

Example:

```text
                    NETRADAR MAP

             ┌─────────────────────────┐
             │                         │
             │       ● Device A        │
             │                         │
             │              ● Device B │
             │                         │
             │  ● Device C             │
             │                         │
             └─────────────────────────┘
```

Each marker can display:

```text
Device Name
Latitude
Longitude
Accuracy
Network Type
Signal Strength
Last Updated
Status
```

---

# 3.8 Location History

NetRadar stores previous GPS measurements.

Example:

```text
Device A

10:00 → Location 1
10:05 → Location 2
10:10 → Location 3
10:15 → Location 4
10:20 → Location 5
```

This information can be displayed as a route.

```text
●────●────●────●────●
```

---

# 3.9 Network History

Network measurements can also be stored historically.

Example:

```text
Time        Network     Signal
10:00       5G          -65 dBm
10:05       5G          -70 dBm
10:10       4G          -82 dBm
10:15       4G          -88 dBm
10:20       5G          -67 dBm
```

This makes it possible to analyze network changes over time.

---

# 3.10 Dashboard

The dashboard provides a centralized interface.

Possible dashboard sections:

```text
------------------------------------------------
| NETRADAR                                     |
------------------------------------------------
| Devices | Online | Offline | Alerts          |
------------------------------------------------
|                                              |
|              LIVE MAP                        |
|                                              |
------------------------------------------------
| Signal | Network | Location | Activity      |
------------------------------------------------
```

---

# 4. System Architecture

The project follows a client-server architecture.

```text
                ┌─────────────────────┐
                │   Authorized Device │
                │                     │
                │ GPS                 │
                │ Network Information │
                │ Signal Information  │
                └──────────┬──────────┘
                           │
                           │ HTTPS / REST API
                           ▼
                ┌─────────────────────┐
                │    Backend Server   │
                │                     │
                │ API                 │
                │ Authentication      │
                │ Validation          │
                │ Business Logic      │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │      Database       │
                │                     │
                │ Devices             │
                │ Locations           │
                │ Network Measurements│
                │ Users               │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │   Web Dashboard     │
                │                     │
                │ Maps                │
                │ Devices             │
                │ Statistics          │
                │ History             │
                └─────────────────────┘
```

---

# 5. Technology Stack

## Backend

Recommended:

```text
Python
Flask / FastAPI
REST API
SQLite
SQLAlchemy
```

For a larger deployment:

```text
PostgreSQL
Redis
Gunicorn
Nginx
```

---

## Frontend

```text
HTML5
CSS3
JavaScript
Bootstrap / Tailwind CSS
Leaflet.js
```

---

## Database

Development:

```text
SQLite
```

Production:

```text
PostgreSQL
```

---

## Maps

A mapping library such as:

```text
Leaflet.js
```

can be used to display GPS coordinates.

A map tile provider must be configured according to its usage policy.

---

# 6. Suggested Project Structure

```text
NetRadar/
│
├── backend/
│   ├── app.py
│   ├── config.py
│   ├── database.py
│   │
│   ├── models/
│   │   ├── device.py
│   │   ├── location.py
│   │   └── network.py
│   │
│   ├── routes/
│   │   ├── devices.py
│   │   ├── locations.py
│   │   └── network.py
│   │
│   └── services/
│       ├── device_service.py
│       ├── location_service.py
│       └── network_service.py
│
├── frontend/
│   ├── index.html
│   ├── dashboard.html
│   │
│   ├── css/
│   │   └── style.css
│   │
│   └── js/
│       ├── app.js
│       ├── map.js
│       └── dashboard.js
│
├── mobile-client/
│   └── ...
│
├── database/
│   └── netradar.db
│
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

---

# 7. Database Design

## 7.1 Devices Table

```text
devices
--------------------------------
id
device_id
device_name
phone_number
platform
model
status
last_seen
created_at
```

Example:

```text
1
DEV-001
My Android
+91XXXXXXXXXX
Android
Samsung
online
2026-08-17 18:50
2026-08-01
```

---

# 7.2 Locations Table

```text
locations
--------------------------------
id
device_id
latitude
longitude
accuracy
altitude
speed
heading
timestamp
```

Relationship:

```text
Device
   │
   ├── Location 1
   ├── Location 2
   ├── Location 3
   └── Location N
```

---

# 7.3 Network Measurements Table

```text
network_measurements
--------------------------------
id
device_id
network_type
operator
mcc
mnc
cell_id
signal_strength
timestamp
```

---

# 8. REST API

The backend can expose APIs such as:

## Register Device

```http
POST /api/devices
```

Example:

```json
{
    "device_name": "My Phone",
    "device_id": "DEV-001",
    "platform": "Android",
    "model": "Samsung"
}
```

---

## Get Devices

```http
GET /api/devices
```

---

## Get Device

```http
GET /api/devices/{device_id}
```

---

## Send Location

```http
POST /api/location
```

Example:

```json
{
    "device_id": "DEV-001",
    "latitude": 12.9716,
    "longitude": 77.5946,
    "accuracy": 10.2
}
```

---

## Get Location History

```http
GET /api/location/{device_id}
```

---

## Send Network Measurement

```http
POST /api/network
```

Example:

```json
{
    "device_id": "DEV-001",
    "network_type": "4G",
    "signal_strength": -76,
    "cell_id": "12345678"
}
```

---

## Get Network History

```http
GET /api/network/{device_id}
```

---

# 9. Data Flow

The complete process is:

```text
1. User installs authorized client
              ↓
2. User grants required permissions
              ↓
3. Client collects available measurements
              ↓
4. Client validates the information
              ↓
5. Client sends HTTPS request
              ↓
6. Backend authenticates device
              ↓
7. Backend validates data
              ↓
8. Database stores measurement
              ↓
9. Dashboard requests data
              ↓
10. Dashboard displays information
```

---

# 10. GPS Accuracy

GPS accuracy depends on:

```text
GPS availability
Satellite visibility
Wi-Fi positioning
Cell positioning
Device hardware
Indoor/outdoor environment
Operating-system settings
Permission settings
```

Example:

```text
Accuracy = 5 m
```

means the reported position has an estimated uncertainty of roughly that order, not that the system knows the phone's position to exactly 5 metres.

---

# 11. Network Accuracy

Network measurements also depend on the device.

For example:

```text
Android Device
      ↓
Telephony APIs
      ↓
Available Cell Information
      ↓
NetRadar Client
      ↓
NetRadar Server
```

The application cannot guarantee that every device will expose:

```text
Cell ID
MCC
MNC
TAC
RSRP
RSRQ
SINR
```

Some fields may be restricted or unavailable.

---

# 12. Security

Security is an important part of NetRadar.

The application should use:

## HTTPS

All communication between clients and server should use HTTPS.

```text
Client
  ↓
HTTPS
  ↓
Server
```

---

## Authentication

Each device should have a unique authentication credential.

Example:

```text
DEVICE_ID
+
DEVICE_TOKEN
```

The server validates the token before accepting measurements.

---

## Authorization

Users should only be able to access devices for which they have permission.

Example:

```text
Admin
 ├── Device A
 ├── Device B
 └── Device C

User
 └── Device A
```

---

## Input Validation

The backend must validate:

```text
Latitude
Longitude
Device ID
Timestamp
Signal strength
Network type
```

For example:

```text
Latitude must be between -90 and +90

Longitude must be between -180 and +180
```

---

# 13. Privacy

NetRadar should follow a privacy-first architecture.

The system should:

* Collect only necessary information.
* Require explicit user permission.
* Clearly explain what is being collected.
* Protect location history.
* Protect authentication tokens.
* Avoid collecting unnecessary personal information.
* Provide deletion mechanisms.
* Log access to sensitive information.

Location history should not be exposed publicly.

---

# 14. What NetRadar Can Track

With a properly authorized client, NetRadar can potentially track:

```text
✓ Device identity
✓ Device online/offline status
✓ GPS location
✓ GPS accuracy
✓ Location history
✓ Network type
✓ Signal strength
✓ Available cell information
✓ Operator information
✓ Connection history
✓ Last-seen timestamp
✓ Device activity
```

Availability depends on the device and permissions.

---

# 15. What NetRadar Cannot Do by Itself

NetRadar cannot magically obtain:

```text
✗ Exact location from phone number alone
✗ Private telecom subscriber location
✗ Secret GPS location
✗ IMSI from a phone number
✗ Carrier-only databases
✗ Another person's location without authorization
✗ Cell-tower triangulation without suitable infrastructure/data
```

A mobile number is primarily an identifier; it is not a GPS coordinate.

---

# 16. Phone Number vs Device Tracking

A common misconception is:

```text
Phone Number
     ↓
GPS Location
```

This does not normally work.

The realistic architecture is:

```text
Phone Number
     ↓
Identify authorized account/device
     ↓
Authorized Client Application
     ↓
GPS / Network APIs
     ↓
NetRadar Server
     ↓
Dashboard
```

Therefore, the phone number can be used as an account/device identifier, while the actual location comes from an authorized device measurement.

---

# 17. Example Dashboard

A production dashboard could contain:

```text
====================================================
                    NETRADAR
====================================================

Devices: 12       Online: 8       Offline: 4

----------------------------------------------------
                    LIVE MAP
----------------------------------------------------

              ● Device A
                       ● Device B

       ● Device C

----------------------------------------------------

DEVICE INFORMATION

Device: DEV-001
Status: ONLINE
Network: 5G
Signal: -68 dBm
Cell ID: 12345678

Location:
Latitude: 12.9716
Longitude: 77.5946
Accuracy: 8m

Last Seen:
18:55:32
====================================================
```

---

# 18. Alerts

The system can optionally provide alerts.

Examples:

```text
Device Offline
Weak Signal
Network Changed
Location Updated
Unexpected Movement
Low GPS Accuracy
Device Reconnected
```

Example:

```text
[ALERT]

Device DEV-001 changed network:

5G → 4G

Time:
18:42:15
```

---

# 19. Analytics

NetRadar can calculate statistics such as:

```text
Average Signal Strength
Network Availability
Online Time
Offline Time
Location Count
Average GPS Accuracy
Network Changes
```

Example:

```text
Network Usage

5G     ███████████████ 60%
4G     ████████        32%
Wi-Fi  ██               8%
```

---

# 20. Installation

## Step 1 — Clone the project

```bash
git clone <your-repository-url>
cd NetRadar
```

---

## Step 2 — Create virtual environment

Windows:

```bash
python -m venv venv
```

Activate:

```bash
venv\Scripts\activate
```

Linux/macOS:

```bash
python3 -m venv venv
source venv/bin/activate
```

---

## Step 3 — Install dependencies

```bash
pip install -r requirements.txt
```

---

# 21. Example requirements.txt

```text
Flask
Flask-SQLAlchemy
Flask-CORS
python-dotenv
PyJWT
requests
```

For FastAPI:

```text
fastapi
uvicorn
sqlalchemy
python-dotenv
PyJWT
```

Use one backend framework rather than installing both unless the project specifically requires both.

---

# 22. Environment Variables

Create:

```text
.env
```

Example:

```env
SECRET_KEY=change-this-secret
DATABASE_URL=sqlite:///netradar.db
```

For production:

```env
DATABASE_URL=postgresql://username:password@host/database
```

Never commit real secrets to GitHub.

---

# 23. Running the Backend

Example Flask command:

```bash
python backend/app.py
```

The API may become available at:

```text
http://127.0.0.1:5000
```

---

# 24. Running the Frontend

If using a simple HTML frontend:

```text
frontend/index.html
```

can be opened through a local development server.

If using a modern frontend such as React:

```bash
npm install
npm run dev
```

---

# 25. Testing the API

Example using curl:

```bash
curl http://127.0.0.1:5000/api/devices
```

Send location:

```bash
curl -X POST http://127.0.0.1:5000/api/location \
-H "Content-Type: application/json" \
-d "{\"device_id\":\"DEV-001\",\"latitude\":12.9716,\"longitude\":77.5946}"
```

---

# 26. Example API Response

```json
{
    "success": true,
    "device_id": "DEV-001",
    "location": {
        "latitude": 12.9716,
        "longitude": 77.5946,
        "accuracy": 8.5
    },
    "timestamp": "2026-08-17T18:55:00"
}
```

---

# 27. Development Roadmap

## Phase 1 — Basic System

```text
✓ Backend
✓ SQLite database
✓ Device registration
✓ REST APIs
✓ Basic dashboard
```

## Phase 2 — Location

```text
✓ GPS collection
✓ Location API
✓ Map integration
✓ Location history
```

## Phase 3 — Network Monitoring

```text
✓ Network type
✓ Signal strength
✓ Operator
✓ Cell information where available
```

## Phase 4 — Advanced Dashboard

```text
✓ Live device status
✓ Charts
✓ Filters
✓ Search
✓ Historical analytics
```

## Phase 5 — Security

```text
✓ Authentication
✓ Device tokens
✓ Role-based access
✓ HTTPS
✓ Audit logs
```

## Phase 6 — Production

```text
✓ PostgreSQL
✓ Docker
✓ Cloud deployment
✓ Monitoring
✓ Backup
✓ Rate limiting
```

---

# 28. Future Enhancements

Potential future features include:

### Mobile Application

Create a native Android client that collects authorized measurements.

```text
Android
   ↓
GPS
   ↓
Telephony APIs
   ↓
NetRadar API
```

### Real-Time Updates

Use:

```text
WebSocket
Socket.IO
```

to update the dashboard without manually refreshing the page.

### Geofencing

Define an allowed area:

```text
       ┌─────────────────┐
       │                 │
       │    Allowed      │
       │     Zone        │
       │       ●         │
       │                 │
       └─────────────────┘
```

The system can generate an alert when an authorized device leaves the configured zone.

### Network Quality Heatmap

Historical signal measurements can be visualized geographically:

```text
Strong Signal
      ↓
● ● ● ●
 ● ● ●
   ●

Weak Signal
      ↓
        ●
      ● ●
    ● ● ●
```

### Multiple Device Monitoring

```text
Organization
     │
     ├── Device 001
     ├── Device 002
     ├── Device 003
     ├── Device 004
     └── Device 005
```

---

# 29. Error Handling

The API should return meaningful errors.

Example:

```json
{
    "success": false,
    "error": "Invalid latitude"
}
```

Possible errors:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Device Not Found
409 Conflict
429 Too Many Requests
500 Internal Server Error
```

---

# 30. Logging

The backend should maintain logs for important events.

Example:

```text
2026-08-17 18:30:01 DEVICE_REGISTERED DEV-001
2026-08-17 18:31:12 LOCATION_RECEIVED DEV-001
2026-08-17 18:32:10 NETWORK_UPDATE DEV-001
2026-08-17 18:35:40 DEVICE_OFFLINE DEV-002
```

Logs help with debugging and security auditing.

---

# 31. Performance Considerations

For a small project:

```text
SQLite
Flask
HTML/CSS/JS
```

is sufficient.

For a larger system:

```text
PostgreSQL
Redis
FastAPI
Nginx
Docker
Cloud infrastructure
```

can be used.

If thousands of measurements are received every minute, the system should use:

* Database indexing
* Batch writes
* Caching
* Background workers
* Rate limiting
* Data retention policies

---

# 32. Security Best Practices

Never store:

```text
Passwords in plain text
API secrets in source code
Private keys in GitHub
Device tokens in frontend source
```

Use:

```text
Environment variables
Password hashing
HTTPS
JWT/session authentication
Role-based authorization
Database access controls
Rate limiting
Audit logging
```

---

# 33. Responsible Use

NetRadar is intended for legitimate use cases such as:

* Personal device monitoring
* Enterprise-owned devices
* Fleet monitoring
* Field-service teams
* Network research
* Authorized testing
* Educational projects
* Network-quality analysis

The system should not be used to secretly monitor or locate another person.

Users should be informed about location collection and provide the required permissions.

---

# 34. Example Use Case

Suppose an organization has five authorized field devices.

```text
Device A → Technician 1
Device B → Technician 2
Device C → Technician 3
Device D → Technician 4
Device E → Technician 5
```

Each device runs the authorized client.

The client periodically sends:

```text
GPS
Network
Signal
Timestamp
Device ID
```

The server stores the information.

The administrator sees:

```text
                NETRADAR

Devices: 5
Online:  4
Offline: 1

Map:
    ● A
        ● B

             ● C

    ● D
```

The administrator can select a device and inspect its authorized measurements and history.

---

# 35. Troubleshooting

## Backend does not start

Check:

```bash
python --version
```

Then:

```bash
pip install -r requirements.txt
```

---

## Database error

Verify:

```text
DATABASE_URL
```

and ensure the application has permission to create/write the database.

---

## Map does not appear

Check:

```text
Internet connection
Map library
Map tile configuration
Browser console
```

---

## GPS unavailable

Check:

```text
Location permission
GPS enabled
Device location settings
Application permissions
Indoor/outdoor environment
```

---

## Cell information unavailable

This may be expected.

Check:

```text
Android version
Application permissions
Device manufacturer
SIM/network configuration
Available Telephony APIs
```

Not every device exposes the same cellular information.

---

# 36. Project Advantages

NetRadar provides:

```text
✓ Centralized monitoring
✓ Historical data
✓ Interactive visualization
✓ REST API architecture
✓ Database persistence
✓ Network monitoring
✓ GPS monitoring
✓ Device status monitoring
✓ Expandable architecture
✓ Suitable for academic projects
```

---

# 37. Limitations

The most important limitations are:

1. GPS data must come from a device with appropriate permission.
2. A phone number alone cannot provide GPS coordinates through this application.
3. Cellular information exposed by Android/iOS varies by device and OS.
4. Cell IDs do not automatically provide exact geographic coordinates.
5. Telecom-level location requires operator-level infrastructure and authorization.
6. Background location collection is subject to mobile operating-system restrictions.
7. GPS accuracy varies with environmental conditions.
8. Network signal values vary depending on radio technology and device implementation.

---

# 38. Project Goal

The long-term goal of NetRadar is to create a reliable network-monitoring platform that combines:

```text
                ┌───────────────┐
                │   DEVICE      │
                └───────┬───────┘
                        │
              ┌─────────┴─────────┐
              │                   │
             GPS              NETWORK
              │                   │
              └─────────┬─────────┘
                        │
                        ▼
                ┌───────────────┐
                │   NETRADAR    │
                │    SERVER     │
                └───────┬───────┘
                        │
              ┌─────────┴─────────┐
              │                   │
          DATABASE            ANALYTICS
              │                   │
              └─────────┬─────────┘
                        │
                        ▼
                ┌───────────────┐
                │   DASHBOARD   │
                └───────────────┘
```

---

# 39. Conclusion

**NetRadar** is a full-stack network and authorized-device monitoring platform.

It demonstrates how GPS measurements, network measurements, backend APIs, databases, authentication, and web visualization can be combined into one practical system.

The project is particularly suitable for demonstrating:

```text
Python
Backend Development
REST APIs
Database Management
Web Development
Mobile Development
GPS
Networking
Data Visualization
Authentication
Cybersecurity
Cloud Deployment
```

The key architectural principle is:

> **The server monitors data that an authorized client supplies; it does not magically derive a person's exact location from a phone number.**

---

# 40. License

Choose an appropriate license before publishing the project publicly.

For example:

```text
MIT License
```

or another license suitable for your project.

---

# 41. Author

**Varshan**

Computer Science & Engineering

Project:

**NetRadar — Mobile Network Monitoring & Authorized Device Tracking**

---

# 42. Final Project Summary

```text
Project Name:
NetRadar

Category:
Mobile Network Monitoring / Authorized Device Tracking

Backend:
Python + Flask/FastAPI

Frontend:
HTML + CSS + JavaScript

Database:
SQLite / PostgreSQL

Mapping:
Leaflet.js

Communication:
REST API / HTTPS

Core Features:
Device Management
GPS Tracking
Network Monitoring
Signal Monitoring
Cell Information
Location History
Dashboard
Analytics
Authentication
Alerts

Primary Purpose:
Authorized device and network monitoring
```
