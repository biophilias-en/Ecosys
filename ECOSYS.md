# Ecosys Platform

## Overview

The Ecosys platform provides an integrated suite of tools for smart building and facility management, combining building management integration, video intercom capabilities, and an IoT ecosystem to enable seamless operations.

---

## Management Integration

The management integration layer connects Ecosys with existing building and facility management systems (BMS/FMS), enabling centralized control and monitoring across all subsystems.

### Key Features

- **Centralized Dashboard**: Unified view of all connected systems including HVAC, lighting, access control, and energy management
- **Third-Party BMS Connectors**: Pre-built integrations for leading BMS platforms (BACnet, Modbus, KNX, LonWorks)
- **Role-Based Access Control (RBAC)**: Granular permissions for facility managers, security personnel, and tenants
- **Audit Logging**: Tamper-evident event logs for all management actions and system changes
- **Alerting & Notifications**: Configurable thresholds trigger alerts via email, SMS, or push notification
- **Scheduling & Automation**: Rule-based automation engine supporting time-based and event-driven policies

### Integration Architecture

```
┌─────────────────────────────────────────────┐
│              Ecosys Management Layer         │
│  ┌──────────┐  ┌──────────┐  ┌───────────┐  │
│  │  REST API│  │ WebSocket│  │  MQTT Bus │  │
│  └────┬─────┘  └────┬─────┘  └─────┬─────┘  │
│       └─────────────┼───────────────┘        │
│                     │                        │
│            ┌────────▼────────┐               │
│            │  Core Engine    │               │
│            └────────┬────────┘               │
│    ┌─────────────────────────────────┐       │
│    │ BACnet │ Modbus │ KNX │ LonWorks│       │
│    └─────────────────────────────────┘       │
└─────────────────────────────────────────────┘
```

### Configuration

```yaml
management:
  bms_connectors:
    - type: bacnet
      host: 192.168.1.10
      port: 47808
    - type: modbus
      host: 192.168.1.20
      port: 502
  rbac:
    roles:
      - name: facility_manager
        permissions: [read, write, configure]
      - name: security_staff
        permissions: [read, monitor]
      - name: tenant
        permissions: [read]
  alerting:
    channels: [email, sms, push]
    default_threshold_minutes: 5
```

---

## Video Intercom

The video intercom module provides two-way audio/video communication between entry points and residents or staff, with full integration into the Ecosys access control and management systems.

### Key Features

- **HD Video Streaming**: 1080p live video feed from door stations and cameras
- **Two-Way Audio**: Full-duplex audio communication between visitor and resident/staff
- **Remote Door Release**: Grant or deny access remotely via web, mobile, or desktop client
- **Visitor Logging**: Automatic capture of visitor images and call records with timestamps
- **Multi-Party Calls**: Support for forwarding calls to multiple recipients simultaneously
- **SIP/VoIP Integration**: Compatible with existing SIP-based phone systems and soft clients
- **Mobile App Support**: iOS and Android apps for remote monitoring and door control
- **Offline Fallback**: Local processing ensures functionality during network outages

### Supported Protocols

| Protocol | Use Case                        |
|----------|---------------------------------|
| SIP/RTP  | Audio/video call signaling      |
| RTSP     | Live video stream delivery      |
| WebRTC   | Browser-based video calls       |
| ONVIF    | IP camera interoperability      |
| OSDP     | Secure door controller comms    |

### Configuration

```yaml
video_intercom:
  stations:
    - id: main_entrance
      model: EcosDoor-Pro
      ip: 192.168.2.10
      resolution: 1080p
      door_relay_pin: 1
    - id: parking_gate
      model: EcosDoor-Compact
      ip: 192.168.2.11
      resolution: 720p
      door_relay_pin: 2
  sip:
    server: sip.example.com
    port: 5060
    transport: TLS
  recording:
    enabled: true
    retention_days: 30
    storage: /mnt/recordings
```

---

## IoT Ecosystem Expansion

The IoT ecosystem expansion module provides a scalable device management platform for onboarding, monitoring, and controlling IoT devices across the facility.

### Key Features

- **Device Onboarding**: Zero-touch provisioning for supported device classes
- **Protocol Support**: Native support for MQTT, CoAP, Zigbee, Z-Wave, and Thread/Matter
- **Device Twin**: Cloud-synchronized shadow state for every connected device
- **Edge Computing**: Local processing nodes reduce latency and cloud dependency
- **OTA Updates**: Over-the-air firmware update management with rollback support
- **Telemetry & Analytics**: Real-time and historical telemetry with configurable retention
- **Geo-fencing & Zones**: Logical grouping of devices by floor, zone, or function
- **Energy Monitoring**: Per-device and aggregate power consumption tracking

### Supported Device Classes

| Class              | Examples                              |
|--------------------|---------------------------------------|
| Environmental      | Temperature, humidity, CO₂ sensors    |
| Lighting           | Smart switches, dimmers, luminaires   |
| Access Control     | Card readers, electric locks, barriers|
| Energy             | Smart meters, PDUs, solar inverters   |
| HVAC               | Thermostats, VAV controllers, AHUs    |
| Safety & Security  | Smoke detectors, motion sensors, sirens|
| Video & Audio      | IP cameras, intercoms, PA systems     |

### Architecture

```
┌──────────────────────────────────────────────────────┐
│                   Ecosys IoT Platform                 │
│  ┌──────────────────────────────────────────────────┐│
│  │              Device Management Cloud              ││
│  │  Device Twin | OTA Manager | Analytics Engine    ││
│  └────────────────────┬─────────────────────────────┘│
│                       │ MQTT / HTTPS                  │
│  ┌────────────────────▼─────────────────────────────┐│
│  │                 Edge Gateway                      ││
│  │  Protocol Bridge | Local Rules | Storage Cache   ││
│  └──────┬───────────┬───────────┬────────────────────┘│
│         │           │           │                     │
│      Zigbee      Z-Wave       Thread/Matter           │
│      ┌──┴──┐   ┌──┴──┐       ┌──┴──┐                 │
│      │Devs │   │Devs │       │Devs │                 │
│      └─────┘   └─────┘       └─────┘                 │
└──────────────────────────────────────────────────────┘
```

### Configuration

```yaml
iot_ecosystem:
  edge_gateway:
    host: 192.168.3.1
    protocols:
      - mqtt:
          port: 1883
          tls: true
      - coap:
          port: 5683
      - zigbee:
          channel: 15
          pan_id: "0x1A2B"
      - zwave:
          region: EU
  device_management:
    twin_sync_interval_seconds: 30
    ota:
      enabled: true
      check_interval_hours: 24
      auto_apply: false
  telemetry:
    retention_days: 90
    aggregation_intervals: [1m, 5m, 1h, 1d]
  zones:
    - name: Floor 1
      devices: [sensor_001, switch_001, meter_001]
    - name: Floor 2
      devices: [sensor_002, switch_002, thermostat_001]
```

---

## Getting Started

### Prerequisites

- Docker 24+ and Docker Compose v2
- 4 vCPU / 8 GB RAM minimum for the management node
- Network access to BMS, intercom stations, and IoT gateway

### Quick Start

```bash
# Clone the repository
git clone https://github.com/wallydewey26-design/Ecosys.git
cd Ecosys

# Copy and edit the configuration
cp config/ecosys.example.yaml config/ecosys.yaml
nano config/ecosys.yaml

# Start the platform
docker compose up -d

# Verify services are running
docker compose ps
```

### Services

| Service                  | Default Port | Description                         |
|--------------------------|--------------|-------------------------------------|
| Management API           | 8080         | REST API for management integration |
| WebSocket Gateway        | 8081         | Real-time event streaming           |
| Video Intercom Gateway   | 8082         | SIP/WebRTC proxy                    |
| IoT MQTT Broker          | 1883 / 8883  | Device messaging (plain / TLS)      |
| Web Dashboard            | 3000         | Unified management UI               |

---

## Security Considerations

- All inter-service communication uses mutual TLS (mTLS)
- Device credentials are stored in an encrypted secrets store
- Video streams are end-to-end encrypted using SRTP
- API authentication uses short-lived JWT tokens with refresh rotation
- Network segmentation is recommended: isolate IoT, intercom, and management VLANs

---

## License

MIT License — see [LICENSE](LICENSE) for details.
