# Node-RED Greenhouse IoT Data Processing System

A Node-RED application for processing IoT sensor data from greenhouse monitoring systems. This application receives MQTT messages from multiple sensor nodes, performs real-time calculations including Vapor Pressure Deficit (VPD), and stores processed data in InfluxDB time-series databases.

## 🏗️ Architecture Overview

### Data Flow
```
MQTT Sensors → Node-RED → Data Processing → InfluxDB Storage
     ↓              ↓           ↓              ↓
  Raw Data    →  Routing  →  VPD Calc  →  Time Series
```

### Components
- **MQTT Broker**: `192.168.20.1:1883`
- **InfluxDB**: `127.0.0.1:8086`
- **Sensor Nodes**: Up to 5 nodes (Node01-Node05)
- **Data Processing**: Real-time VPD calculations and averaging

## 📊 Sensor Data Types

### Environmental Sensors
- **Air Temperature** (`Air_Temp`): Ambient air temperature
- **Air Humidity** (`Air_Rh`): Relative humidity percentage
- **Leaf Temperature** (`Leaf_temp`): Plant leaf surface temperature
- **Light PAR** (`Light_Par`): Photosynthetically Active Radiation

### Substrate Sensors
- **Bag Temperature** (`Bag_Temp`): Growing medium temperature
- **Bag Humidity** (`Bag_Rh1-4`): Multiple humidity sensors in growing medium

### Irrigation Sensors
- **Drip Weight** (`drip_weight`): Irrigation system monitoring

## 🔧 Setup Instructions

### Prerequisites
- Node.js and npm installed
- Node-RED installed globally: `npm install -g node-red`
- InfluxDB server running on `127.0.0.1:8086`
- MQTT broker accessible at `192.168.20.1:1883`

### Installation
1. Clone or download this project
2. Install dependencies:
   ```bash
   npm install
   ```
3. Start Node-RED:
   ```bash
   node-red
   ```
4. Access the editor at `http://localhost:1880`

### Configuration

#### InfluxDB Setup
- **Organization**: `iot-agriculture`
- **Buckets**:
  - `SensorData`: Raw sensor data storage
  - `CalculatedData`: Processed calculations and averages

#### MQTT Topics
- **Pattern**: `greenhouse/+/node/+/data`
- **Format**: JSON payload with sensor readings
- **Example Topic**: `greenhouse/001/node/Node01/data`

## 📈 Data Processing Pipeline

### 1. Data Routing
Incoming MQTT messages are routed based on node ID:
- Node01-Node04: Full processing (VPD + averaging)
- Node05: Basic storage only

### 2. VPD Calculations
Vapor Pressure Deficit (VPD) is calculated using scientific formulas:
```
es_air = 0.6108 * exp((17.27 * T_air) / (T_air + 237.3))
es_leaf = 0.6108 * exp((17.27 * T_leaf) / (T_leaf + 237.3))
ea = (RH / 100) * es_air
VPD = max(0, es_leaf - ea)
```

### 3. Data Averaging
Cross-node averages are computed for:
- All sensor readings
- Combined Bag_Rh sensors
- VPD and related calculations

## 🗄️ Data Storage

### InfluxDB Measurements
- **`greenhouse_data_clean`**: Raw sensor data
- **`calculated_data`**: VPD calculations and averages

### Tags
- `greenhouse_id`: Greenhouse identifier
- `node_id`: Sensor node identifier
- `type`: Data type (e.g., "average")

## 🔍 Monitoring & Debugging

### Log Levels
- **Info**: General application flow
- **Warn**: VPD calculations and averaging results
- **Error**: Invalid data or calculation errors

### Key Metrics
- VPD values per node
- Cross-node averages
- Data processing throughput

## 🚀 Usage

### Starting the Application
1. Ensure InfluxDB is running
2. Verify MQTT broker connectivity
3. Start Node-RED: `node-red`
4. Deploy the flows in the editor

### Data Flow
1. Sensor nodes publish data to MQTT topics
2. Node-RED receives and processes the data
3. VPD calculations are performed in real-time
4. Data is stored in InfluxDB for analysis

## 🔧 Configuration Files

- **`flows.json`**: Main flow configuration
- **`flows_cred.json`**: Encrypted credentials
- **`settings.js`**: Node-RED runtime settings
- **`package.json`**: Dependencies and project metadata

## 📋 Dependencies

- **node-red-contrib-influxdb**: ~0.7.0
- **Node-RED Core**: Standard installation

## 🌱 Agricultural Applications

This system is designed for precision agriculture, specifically:
- **Greenhouse Climate Control**: Real-time environmental monitoring
- **Plant Health Optimization**: VPD-based irrigation decisions
- **Data-Driven Farming**: Historical analysis and trend identification
- **Automated Irrigation**: Sensor-based watering systems

## 🔒 Security Considerations

- Credentials are encrypted in `flows_cred.json`
- MQTT broker should be secured with authentication
- InfluxDB should be configured with proper access controls
- Consider HTTPS for production deployments

## 📞 Support

For issues or questions:
1. Check Node-RED logs for error messages
2. Verify MQTT broker connectivity
3. Ensure InfluxDB is running and accessible
4. Review sensor data format matches expected schema

## 📄 License

This project is for internal use. Please ensure compliance with your organization's data handling policies.