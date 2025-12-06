# Web Dashboard Repository

## Overview
The application is a real-time monitoring dashboard built with a backend and a frontend using HTML, CSS, and JavaScript with Chart.js, while Azure Cosmos DB is accessed via the @azure/cosmos SDK. It displays live sensor data from three locations with dedicated cards, includes safety status badges, and automatically refreshes every 30 seconds. The dashboard also provides historical trend charts for the past hour and shows the overall system status, offering a comprehensive view of environmental conditions and safety at a glance.

## Prerequisites
**Software:**

- Node.js and npm installed

- A modern web browser for accessing the frontend

**Libraries/SDKs:**

- @azure/cosmos for connecting to Azure Cosmos DB

- Express.js

- Chart.js

**Cloud Resources:**

- An Azure Cosmos DB account with a database and container set up

## Installation

1. Fork and Clone the Repository
```bash
git clone https://github.com/<Github-Username>/25F_CST8916_Final_Project_Web-Dashboard

cd 25F_CST8916_Final_Project_Web-Dashboard
```
2. Install Backend Dependencies
```bash
npm install
```
## Configuration
```ini
COSMOS_ENDPOINT="your-cosmosdb-endpoint"
COSMOS_KEY="your-cosmosdb-key"
COSMOS_DATABASE="your-database-name"
COSMOS_CONTAINER="your-container-name"
```
## Deploying the Web Application Locally
- In the terminal, enter:
```bash
node server.js
```
- Open the dashboard frontend through: http://localhost:3000

## API Endpoints

### 1. Get Latest Sensor Data

Endpoint: `GET /api/latest`

Description: Fetches the most recent readings for all monitored locations (Dow's Lake, Fifth Avenue, NAC).

Example Request:
```http
GET /api/latest HTTP/1.1
Host: localhost:3000
```

Example Response:
```json
{
  "success": true,
  "timestamp": "2025-12-06T18:30:00.123Z",
  "data": [
    {
      "location": "Dow's Lake",
      "ice_thickness": 32.5,
      "surf_temperature": -3.2,
      "snow_accumulation": 12.4,
      "ext_temperature": -8.7,
      "SafetyStatus": "Safe",
      "windowEndTime": "2025-12-06T18:25:00Z"
    },
    {
      "location": "Fifth Avenue",
      "ice_thickness": 28.1,
      "surf_temperature": -1.8,
      "snow_accumulation": 15.0,
      "ext_temperature": -5.2,
      "SafetyStatus": "Caution",
      "windowEndTime": "2025-12-06T18:25:00Z"
    }
  ]
}
```
### 2. Get Historical Data for a Location

Endpoint: `GET /api/history/:location`

Description: Retrieves telemetry data for the last hour (or configurable limit) for a specific location. Useful for trend charts.

Example Request:
```http
GET /api/history/DowsLake?limit=12 HTTP/1.1
Host: localhost:3000
```

Example Response:
```json
{
  "success": true,
  "location": "Dow's Lake",
  "data": [
    { "windowEndTime": "2025-12-06T17:30:00Z", "ice_thickness": 31.2, "SafetyStatus": "Safe" },
    { "windowEndTime": "2025-12-06T17:35:00Z", "ice_thickness": 31.5, "SafetyStatus": "Safe" },
    { "windowEndTime": "2025-12-06T17:40:00Z", "ice_thickness": 32.0, "SafetyStatus": "Safe" }
  ]
}
```
### 3. Get Overall System Status

Endpoint: `GET /api/status`

Description: Returns the overall safety status of the system (Safe, Caution, Unsafe) based on all locations, along with the latest status of each location.

Example Request:
```http
GET /api/status HTTP/1.1
Host: localhost:3000
```

Example Response:
```json
{
  "success": true,
  "overallStatus": "Caution",
  "locations": [
    { "location": "Dow's Lake", "SafetyStatus": "Safe", "windowEndTime": "2025-12-06T18:25:00Z" },
    { "location": "Fifth Avenue", "SafetyStatus": "Caution", "windowEndTime": "2025-12-06T18:25:00Z" },
    { "location": "NAC", "SafetyStatus": "Safe", "windowEndTime": "2025-12-06T18:25:00Z" }
  ]
}
```
### 4. Get All Data (Debugging)

Endpoint: `GET /api/all`

Description: Returns all telemetry records from Cosmos DB, sorted by timestamp. Useful for debugging or full data export.

Example Request:
```http
GET /api/all HTTP/1.1
Host: localhost:3000
```

Example Response:
```json
{
  "success": true,
  "count": 120,
  "data": [
    { "location": "Dow's Lake", "ice_thickness": 32.5, "SafetyStatus": "Safe", "windowEndTime": "2025-12-06T18:25:00Z" },
    { "location": "Fifth Avenue", "ice_thickness": 28.1, "SafetyStatus": "Caution", "windowEndTime": "2025-12-06T18:25:00Z" }
  ]
}
```
### 5. Health Check

Endpoint: `GET /health`

Description: Returns server health and configuration status, including Cosmos DB setup.

Example Request:
```http
GET /health HTTP/1.1
Host: localhost:3000
```

Example Response:
```json
{
  "status": "healthy",
  "timestamp": "2025-12-06T18:30:00.123Z",
  "cosmosdb": {
    "endpoint": "configured",
    "database": "RideauCanalDB",
    "container": "SensorData"
  }
}
```
### 6. Serve Dashboard

Endpoint: `GET /`

Description: Serves the frontend HTML dashboard (index.html).

Example Request:
```http
GET / HTTP/1.1
Host: localhost:3000
```

Example Response: **HTML page displaying the dashboard interface.**

### 7. Error Response Example

When an endpoint fails, it returns:
```json
{
  "success": false,
  "error": "Descriptive error message"
}
```

with HTTP status `500`.

## Deployment to Azure App Service
1. Create App Services
- In the Azure Portal, Select App Services
- Create a `Web App`
- Fill in: `Basics`
    - Resource Group: `Your resource group`
    - Name: `App Name`
    - Publish: Code
    - Runtime stack: `Node 24 LTS`
    - Region: `Canada Central`
    - Pricing Plan: `Free`
    - Zone redundancy: `Disabled`
- Review and Create
2. Configure Deployment with GitHub
- In the created Web App, Go to Deployment Center.
- Source: `GitHub`
- Sign in as: `<your github username>`
- Organization: `<your github username>`
- Repository: `25F_CST8916_Final_Project_Web-Dashboard`
- Branch: `main`
- Click `Save`
3. Create Environment Variables
- In the Environment variables tab
- Click `Add`
    - Name: `COSMOS_CONTAINER`
    - Value: `SensorAggregations`
    - Click Apply
- Repeat steps for other environment variables:
    - Database
        - Name: `COSMOS_DATABASE`
        - Value: `RideauCanalDB`
    - Cosmos DB URI
        - Name: `ENDPOINT`
        - Value: `<web-app-name>.canadacentral-01.azurewebsites.net`
    - Cosmos DB Primary Key
        - Name: `KEY`
        - Value: `<CosmosDBPrimaryKey>`
    - Port
        - Name: `PORT`
        - Value: `3000`


## Dashboard Features
- Real-time updates
- Charts and visualizations
- Safety status indicators

## Troubleshooting
- Make sure to install dependencies with `npm install`
- Make sure to create/change the environment variables when testing locally and deploying in App Services
