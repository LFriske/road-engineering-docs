# Backend Structure Document - road.engineering

This document outlines the architecture, API endpoints, data flow, and server-side logic for the road.engineering platform. It provides a comprehensive guide for implementing the backend services that will power the website's data processing, storage, and retrieval capabilities.

## 1. Architecture Overview

The road.engineering backend will follow a layered architecture design with clear separation of concerns:

```
┌───────────────────────────────────────────────────────────────┐
│                      Client Application                       │
└───────────────────────────────────────────────────────────────┘
                               ▲
                               │ HTTPS/REST
                               ▼
┌───────────────────────────────────────────────────────────────┐
│                         API Gateway                           │
└───────────────────────────────────────────────────────────────┘
          ▲                    ▲                    ▲
          │                    │                    │
          ▼                    ▼                    ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│  Crash Data   │     │  Traffic Data │     │  AADT Data    │
│   Service     │     │   Service     │     │   Service     │
└───────────────┘     └───────────────┘     └───────────────┘
          ▲                    ▲                    ▲
          │                    │                    │
          ▼                    ▼                    ▼
┌───────────────────────────────────────────────────────────────┐
│                      Database Layer                           │
│        (PostgreSQL + PostGIS / Redis Cache / File Store)      │
└───────────────────────────────────────────────────────────────┘
                               ▲
                               │ ETL Pipeline
                               ▼
┌───────────────────────────────────────────────────────────────┐
│                    External Data Sources                       │
│   Queensland Crash Data / Traffic Data / AADT Census Data     │
└───────────────────────────────────────────────────────────────┘
```

### Key Components

1. **API Gateway**: 
   - Single entry point for all client requests
   - Handles authentication, rate limiting, and request routing
   - Manages CORS and security policies

2. **Microservices**:
   - **Crash Data Service**: Manages road crash data access and processing
   - **Traffic Data Service**: Handles real-time and historical traffic data
   - **AADT Data Service**: Provides Average Annual Daily Traffic information
   - **Report Generation Service**: Creates custom reports for selected roads

3. **Database Layer**:
   - Primary data store (PostgreSQL + PostGIS)
   - Caching layer (Redis)
   - File storage for generated reports (cloud storage)

4. **ETL Pipeline**:
   - Scheduled jobs for importing and processing external data
   - Data transformation and normalization
   - Data quality validation and error handling

## 2. API Endpoints

The road.engineering API will follow RESTful design principles with consistent URL patterns, HTTP methods, and status codes. All API endpoints will be versioned to support backward compatibility as the platform evolves.

### Base URL

```
https://api.road.engineering/v1/
```

### Authentication

```
Authorization: Bearer {jwt_token}
```

### Core Endpoints

#### Crash Data Endpoints

```
GET /crashes
```
Retrieve crash data with filtering options

**Query Parameters:**
- `bounds`: Map bounds (north,south,east,west) for spatial filtering
- `road_id`: Specific road identifier
- `severity`: Crash severity level(s) - "Fatal", "Serious", "Minor"
- `start_date`: Start date for temporal filtering (YYYY-MM-DD)
- `end_date`: End date for temporal filtering (YYYY-MM-DD)
- `limit`: Maximum number of records to return (default: 1000)
- `offset`: Pagination offset (default: 0)

**Response Example:**
```json
{
  "data": [
    {
      "id": "crash_123456",
      "coordinates": {
        "latitude": -27.4698,
        "longitude": 153.0251
      },
      "severity": "Serious",
      "date": "2022-03-15T14:30:00Z",
      "road_name": "Bruce Highway",
      "casualties": 2,
      "vehicles": 2,
      "crash_nature": "Rear-end",
      "road_feature": "Intersection",
      "speed_limit": 80,
      "weather_condition": "Fine",
      "lighting_condition": "Daylight"
    },
    // More crash records...
  ],
  "metadata": {
    "total_count": 5284,
    "returned_count": 1000,
    "is_preliminary": false
  }
}
```

```
GET /crashes/{crash_id}
```
Retrieve detailed information about a specific crash

**Response Example:**
```json
{
  "id": "crash_123456",
  "coordinates": {
    "latitude": -27.4698,
    "longitude": 153.0251
  },
  "severity": "Serious",
  "date": "2022-03-15T14:30:00Z",
  "road_name": "Bruce Highway",
  "casualties": 2,
  "vehicles": 2,
  "crash_nature": "Rear-end",
  "road_feature": "Intersection",
  "speed_limit": 80,
  "weather_condition": "Fine",
  "lighting_condition": "Daylight",
  "road_surface": "Dry",
  "road_condition": "Good",
  "contributing_factors": [
    "Inattention",
    "Following too closely"
  ],
  "vehicle_types": [
    "Car",
    "SUV"
  ],
  "road_users": [
    {
      "type": "Driver",
      "age_group": "30-39",
      "gender": "Male",
      "injury_severity": "Minor"
    },
    {
      "type": "Driver",
      "age_group": "20-29",
      "gender": "Female",
      "injury_severity": "Serious"
    },
    {
      "type": "Passenger",
      "age_group": "0-16",
      "gender": "Female",
      "injury_severity": "Minor"
    }
  ]
}
```

#### Traffic Data Endpoints

```
GET /traffic
```
Retrieve current traffic information

**Query Parameters:**
- `bounds`: Map bounds for spatial filtering
- `type`: Traffic incident type(s) - "Congestion", "Roadwork", "Accident", "Closure"
- `status`: Incident status - "Active", "Cleared", "Scheduled"

**Response Example:**
```json
{
  "data": [
    {
      "id": "traffic_78901",
      "coordinates": {
        "latitude": -27.4618,
        "longitude": 153.0241
      },
      "type": "Congestion",
      "status": "Active",
      "updated_at": "2023-05-10T08:45:00Z",
      "description": "Heavy traffic on Gateway Motorway northbound",
      "location": "Gateway Motorway near Airport Link",
      "estimated_duration": 60,
      "impact_level": "Moderate"
    },
    // More traffic incidents...
  ],
  "metadata": {
    "total_count": 42,
    "returned_count": 42,
    "last_updated": "2023-05-10T08:50:00Z"
  }
}
```

#### AADT Data Endpoints

```
GET /aadt
```
Retrieve Annual Average Daily Traffic data

**Query Parameters:**
- `bounds`: Map bounds for spatial filtering
- `road_id`: Specific road identifier
- `min_count`: Minimum traffic count
- `max_count`: Maximum traffic count
- `year`: Specific year for AADT data

**Response Example:**
```json
{
  "data": [
    {
      "id": "aadt_24680",
      "coordinates": {
        "latitude": -27.4701,
        "longitude": 153.0261
      },
      "road_name": "Pacific Motorway",
      "road_id": "M1",
      "count": 145000,
      "year": 2022,
      "location_description": "Pacific Motorway at South Brisbane",
      "heavy_vehicle_percentage": 12.5,
      "peak_hour_percentage": 8.2
    },
    // More AADT data points...
  ],
  "metadata": {
    "total_count": 824,
    "returned_count": 824,
    "year_range": [2018, 2022]
  }
}
```

#### Road Data Endpoints

```
GET /roads
```
Retrieve road information

**Query Parameters:**
- `bounds`: Map bounds for spatial filtering
- `search`: Text search for road names
- `type`: Road type(s) - "Highway", "Motorway", "Arterial", "Local"

**Response Example:**
```json
{
  "data": [
    {
      "id": "road_13579",
      "name": "Bruce Highway",
      "road_id": "M1",
      "type": "Highway",
      "length_km": 1679,
      "start_point": {
        "latitude": -27.4698,
        "longitude": 153.0251,
        "description": "Brisbane CBD"
      },
      "end_point": {
        "latitude": -16.9186,
        "longitude": 145.7781,
        "description": "Cairns"
      }
    },
    // More road records...
  ],
  "metadata": {
    "total_count": 156,
    "returned_count": 156
  }
}
```

```
GET /roads/{road_id}
```
Retrieve detailed information about a specific road

**Response Example:**
```json
{
  "id": "road_13579",
  "name": "Bruce Highway",
  "road_id": "M1",
  "type": "Highway",
  "length_km": 1679,
  "regions": ["Brisbane", "Sunshine Coast", "Wide Bay", "Mackay", "Townsville", "Cairns"],
  "segments": [
    {
      "id": "segment_11111",
      "start_point": {
        "latitude": -27.4698,
        "longitude": 153.0251,
        "description": "Brisbane CBD"
      },
      "end_point": {
        "latitude": -26.6500,
        "longitude": 153.0667,
        "description": "Sunshine Coast"
      },
      "length_km": 100.2,
      "lanes": 4,
      "speed_limit": 110,
      "aadt": 85000
    },
    // More road segments...
  ],
  "crash_statistics": {
    "total_crashes": 1245,
    "fatal_crashes": 28,
    "serious_crashes": 387,
    "minor_crashes": 830,
    "common_crash_types": [
      {"type": "Rear-end", "count": 456},
      {"type": "Run-off-road", "count": 321},
      {"type": "Head-on", "count": 89}
    ],
    "yearly_trend": [
      {"year": 2018, "total": 268},
      {"year": 2019, "total": 275},
      {"year": 2020, "total": 231},
      {"year": 2021, "total": 243},
      {"year": 2022, "total": 228}
    ]
  }
}
```

#### Report Generation Endpoints

```
POST /reports
```
Generate a crash data report for a specific road

**Request Body:**
```json
{
  "road_id": "road_13579",
  "start_date": "2018-01-01",
  "end_date": "2022-12-31",
  "include_charts": true,
  "include_crash_details": true,
  "format": "pdf"
}
```

**Response Example:**
```json
{
  "report_id": "report_24680",
  "status": "processing",
  "estimated_completion_time": "2023-05-10T09:05:00Z",
  "road_name": "Bruce Highway",
  "date_range": {
    "start_date": "2018-01-01",
    "end_date": "2022-12-31"
  }
}
```

```
GET /reports/{report_id}
```
Retrieve status or download a generated report

**Response Example (when processing):**
```json
{
  "report_id": "report_24680",
  "status": "processing",
  "progress": 65,
  "estimated_completion_time": "2023-05-10T09:05:00Z"
}
```

**Response Example (when complete):**
```json
{
  "report_id": "report_24680",
  "status": "complete",
  "created_at": "2023-05-10T09:05:00Z",
  "expires_at": "2023-05-17T09:05:00Z",
  "download_url": "https://storage.road.engineering/reports/report_24680.pdf",
  "format": "pdf",
  "size_bytes": 2458600,
  "road_name": "Bruce Highway",
  "date_range": {
    "start_date": "2018-01-01",
    "end_date": "2022-12-31"
  },
  "summary": {
    "total_crashes": 1245,
    "fatal_crashes": 28,
    "serious_crashes": 387,
    "minor_crashes": 830
  }
}
```
