# Face Recognition and Locked Face Tracking System

An end-to-end computer vision system that performs real-time face detection and tracking using PC-based vision processing, MQTT messaging, ESP8266 microcontroller, and servo motor control.

## System Description

This system implements a closed-loop face tracking mechanism where:
- A PC-based vision module continuously captures video and detects faces using OpenCV's Haar cascade classifier
- Face position data is published via MQTT to control a servo motor
- An ESP8266 microcontroller receives movement commands and adjusts a servo to track the detected face
- A web-based dashboard provides real-time monitoring of the system status

## Architecture

### Components

1. **Vision Node** (`vision-node/`)
   - Captures video from webcam
   - Detects faces using Haar cascade classifier
   - Determines face position relative to frame center
   - Publishes movement commands via MQTT

2. **ESP8266 Controller** (`esp8266/`)
   - Connects to WiFi network
   - Subscribes to MQTT movement commands
   - Controls servo motor position (0-180 degrees)
   - Implements 5-degree incremental movements

3. **Backend Server** (`backend/`)
   - WebSocket server for real-time dashboard communication
   - MQTT message forwarding to web clients
   - Handles multiple concurrent dashboard connections

4. **Live Dashboard** (`dashboard/`)
   - Real-time display of face tracking status
   - Shows confidence levels and timestamps
   - WebSocket-based live updates

## MQTT Topics

The system uses the following MQTT topic structure:

### Primary Topic
- **`vision/team05/movement`** - Main communication channel for movement commands

### Message Format
```json
{
    "status": "MOVE_LEFT" | "MOVE_RIGHT" | "CENTERED" | "NO_FACE",
    "confidence": 0.9,
    "timestamp": 1642123456
}
```

### Status Values
- `MOVE_LEFT`: Face detected on the left side of frame
- `MOVE_RIGHT`: Face detected on the right side of frame  
- `CENTERED`: Face centered within threshold (±50 pixels)
- `NO_FACE`: No face detected in current frame

## Live Dashboard URL

Access the real-time monitoring dashboard at:
```
http://localhost:8000
```

The dashboard displays:
- Current tracking status
- Face detection confidence level
- Real-time timestamp updates

## Network Configuration

### MQTT Broker
- **IP Address**: 10.12.73.101
- **Port**: 1883
- **Protocol**: MQTT

### WebSocket Server
- **IP Address**: 0.0.0.0 (all interfaces)
- **Port**: 9002
- **Protocol**: WebSocket

### WiFi Configuration (ESP8266)
- **SSID**: Wireless1
- **Password**: @RcaNyabihu2023

## Installation and Setup

### Prerequisites
- Python 3.7+
- OpenCV
- Paho-MQTT
- WebSockets
- ESP8266 with MicroPython
- MQTT Broker (Mosquitto recommended)
- Servo motor connected to ESP8266 pin 5

### Python Dependencies
```bash
pip install opencv-python paho-mqtt websockets
```

### Running the System

1. **Start MQTT Broker**
   ```bash
   mosquitto -p 1883
   ```

2. **Start Backend Server**
   ```bash
   cd backend
   python backend.py
   ```

3. **Start Vision Node**
   ```bash
   cd vision-node
   python vision_node.py
   ```

4. **Upload ESP8266 Code**
   - Flash `esp8266/main.py` to ESP8266
   - Ensure WiFi credentials are configured

5. **Access Dashboard**
   - Open `dashboard/index.html` in web browser
   - Or serve via HTTP server: `python -m http.server 8000`

## Technical Specifications

### Face Detection Parameters
- **Classifier**: Haar Cascade (frontal face default)
- **Scale Factor**: 1.3
- **Min Neighbors**: 5
- **Center Threshold**: ±50 pixels from frame center

### Servo Control
- **PWM Frequency**: 50 Hz
- **Duty Cycle Range**: 40-115 (0-180 degrees)
- **Movement Increment**: 5 degrees per command
- **Angle Limits**: 0-180 degrees

### Performance
- **Video Processing**: Real-time (30 FPS target)
- **MQTT Latency**: <100ms (local network)
- **WebSocket Updates**: Real-time
- **Servo Response**: Immediate upon command receipt

## System Workflow

1. Vision node captures webcam frames continuously
2. Face detection algorithm processes each frame
3. Face position calculated relative to frame center
4. Movement command published to MQTT topic
5. ESP8266 receives command via MQTT subscription
6. Servo motor adjusted to track face position
7. Status data forwarded to backend via MQTT
8. Backend broadcasts to dashboard via WebSocket
9. Dashboard updates display in real-time

## Troubleshooting

### Common Issues
- **No face detection**: Check lighting conditions and camera positioning
- **MQTT connection failed**: Verify broker IP and network connectivity
- **Servo not responding**: Check PWM wiring and power supply
- **Dashboard not updating**: Ensure WebSocket server is running

### Debug Mode
Enable debug output by modifying log levels in individual components.

## Team Information

**Team ID**: team05
**Project**: Face Recognition and Locked Face Tracking System
**Assessment**: Vision Control Module - Term 2
