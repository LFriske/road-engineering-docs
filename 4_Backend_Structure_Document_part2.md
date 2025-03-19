## 3. Data Models (continued)

### PostgreSQL Database Schema (continued)

#### AADT Data Table (continued)

```sql
CREATE TABLE aadt_data (
    id VARCHAR(50) PRIMARY KEY,
    road_id VARCHAR(50),
    road_segment_id VARCHAR(50),
    latitude DECIMAL(10, 8) NOT NULL,
    longitude DECIMAL(11, 8) NOT NULL,
    geom GEOMETRY(Point, 4326) NOT NULL,
    count INTEGER NOT NULL,
    year INTEGER NOT NULL,
    road_name VARCHAR(100),
    location_description VARCHAR(200),
    heavy_vehicle_percentage DECIMAL(5, 2),
    peak_hour_percentage DECIMAL(5, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (road_id) REFERENCES roads(id) ON DELETE SET NULL,
    FOREIGN KEY (road_segment_id) REFERENCES road_segments(id) ON DELETE SET NULL
);

-- Add spatial index
CREATE INDEX aadt_data_geom_idx ON aadt_data USING GIST (geom);

-- Add road reference index
CREATE INDEX aadt_data_road_idx ON aadt_data (road_id);

-- Add year index for temporal queries
CREATE INDEX aadt_data_year_idx ON aadt_data (year);
```

#### Traffic Incident Table

```sql
CREATE TABLE traffic_incidents (
    id VARCHAR(50) PRIMARY KEY,
    latitude DECIMAL(10, 8) NOT NULL,
    longitude DECIMAL(11, 8) NOT NULL,
    geom GEOMETRY(Point, 4326) NOT NULL,
    type VARCHAR(20) NOT NULL,
    status VARCHAR(20) NOT NULL,
    road_id VARCHAR(50),
    road_name VARCHAR(100),
    description TEXT,
    location TEXT,
    started_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL,
    cleared_at TIMESTAMP,
    estimated_duration INTEGER,
    impact_level VARCHAR(20),
    source VARCHAR(50),
    CONSTRAINT type_check CHECK (type IN ('Congestion', 'Roadwork', 'Accident', 'Closure')),
    CONSTRAINT status_check CHECK (status IN ('Active', 'Cleared', 'Scheduled'))
);

-- Add spatial index
CREATE INDEX traffic_incidents_geom_idx ON traffic_incidents USING GIST (geom);

-- Add status and type combined index
CREATE INDEX traffic_incidents_status_type_idx ON traffic_incidents (status, type);

-- Add temporal index
CREATE INDEX traffic_incidents_time_idx ON traffic_incidents (started_at, cleared_at);
```

#### Generated Reports Table

```sql
CREATE TABLE reports (
    id VARCHAR(50) PRIMARY KEY,
    road_id VARCHAR(50),
    start_date DATE,
    end_date DATE,
    format VARCHAR(10) NOT NULL,
    include_charts BOOLEAN DEFAULT TRUE,
    include_crash_details BOOLEAN DEFAULT TRUE,
    status VARCHAR(20) NOT NULL,
    progress INTEGER DEFAULT 0,
    storage_path VARCHAR(255),
    file_size_bytes INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP,
    expires_at TIMESTAMP,
    CONSTRAINT format_check CHECK (format IN ('pdf', 'csv')),
    CONSTRAINT status_check CHECK (status IN ('processing', 'complete', 'failed', 'expired')),
    FOREIGN KEY (road_id) REFERENCES roads(id) ON DELETE SET NULL
);

-- Add status index
CREATE INDEX reports_status_idx ON reports (status);

-- Add road reference index
CREATE INDEX reports_road_idx ON reports (road_id);

-- Add expiration index
CREATE INDEX reports_expires_idx ON reports (expires_at);
```

## 4. Geospatial Data Handling

### Coordinate Systems

The road.engineering platform will use WGS84 (EPSG:4326) as the primary coordinate reference system for all geospatial data. This ensures compatibility with common web mapping libraries like Leaflet.js and follows the standards of most modern geospatial web applications.

### Spatial Queries

PostGIS provides powerful capabilities for spatial operations. Here are examples of key spatial queries used in the application:

#### Finding Crashes Within Map Bounds

```sql
SELECT id, latitude, longitude, severity, crash_date, road_name
FROM crashes
WHERE geom && ST_MakeEnvelope($west, $south, $east, $north, 4326)
AND crash_date BETWEEN $start_date AND $end_date
ORDER BY crash_date DESC
LIMIT $limit OFFSET $offset;
```

#### Finding Crashes Along a Road

```sql
SELECT c.id, c.latitude, c.longitude, c.severity, c.crash_date, c.road_name
FROM crashes c
JOIN roads r ON ST_DWithin(c.geom, r.geom, 0.001)
WHERE r.id = $road_id
AND c.crash_date BETWEEN $start_date AND $end_date
ORDER BY c.crash_date DESC;
```

#### Clustering Crashes for Map Visualization

```sql
SELECT 
    ST_ClusterDBSCAN(geom, eps := 0.01, minpoints := 3) OVER () AS cluster_id,
    COUNT(*) OVER (PARTITION BY ST_ClusterDBSCAN(geom, eps := 0.01, minpoints := 3) OVER ()) AS cluster_count,
    AVG(ST_X(geom)) OVER (PARTITION BY ST_ClusterDBSCAN(geom, eps := 0.01, minpoints := 3) OVER ()) AS cluster_longitude,
    AVG(ST_Y(geom)) OVER (PARTITION BY ST_ClusterDBSCAN(geom, eps := 0.01, minpoints := 3) OVER ()) AS cluster_latitude,
    id, severity, crash_date
FROM crashes
WHERE geom && ST_MakeEnvelope($west, $south, $east, $north, 4326);
```

### Spatial Analysis

The backend will support various spatial analysis capabilities to enhance the platform's reporting features:

#### Crash Hotspot Identification

```sql
SELECT 
    ST_HexagonGrid(0.01, ST_MakeEnvelope($west, $south, $east, $north, 4326)) AS hex_cell,
    COUNT(c.id) AS crash_count,
    SUM(CASE WHEN c.severity = 'Fatal' THEN 5
             WHEN c.severity = 'Serious' THEN 3
             ELSE 1 END) AS weighted_count
FROM crashes c
WHERE c.geom && ST_MakeEnvelope($west, $south, $east, $north, 4326)
AND c.crash_date BETWEEN $start_date AND $end_date
GROUP BY hex_cell
HAVING COUNT(c.id) > 5
ORDER BY weighted_count DESC;
```

#### Road Segment Risk Assessment

```sql
SELECT 
    rs.id AS segment_id,
    rs.road_id,
    rs.length_km,
    COUNT(c.id) AS crash_count,
    COUNT(c.id) / rs.length_km AS crash_density,
    SUM(CASE WHEN c.severity = 'Fatal' THEN 1 ELSE 0 END) AS fatal_crashes,
    SUM(CASE WHEN c.severity = 'Serious' THEN 1 ELSE 0 END) AS serious_crashes,
    (SUM(CASE WHEN c.severity = 'Fatal' THEN 5
              WHEN c.severity = 'Serious' THEN 3
              ELSE 1 END) / rs.length_km) / NULLIF(a.count / 10000, 0) AS risk_score
FROM road_segments rs
LEFT JOIN crashes c ON ST_DWithin(rs.geom, c.geom, 0.001)
                   AND c.crash_date BETWEEN $start_date AND $end_date
LEFT JOIN LATERAL (
    SELECT AVG(count) AS count 
    FROM aadt_data 
    WHERE road_segment_id = rs.id
    AND year BETWEEN EXTRACT(YEAR FROM $start_date) AND EXTRACT(YEAR FROM $end_date)
) a ON true
WHERE rs.road_id = $road_id
GROUP BY rs.id, rs.road_id, rs.length_km, a.count
ORDER BY risk_score DESC;
```

## 5. Data Processing Strategies

### ETL Pipeline

The ETL (Extract, Transform, Load) pipeline will handle data imports from Queensland Government datasets with the following steps:

#### 1. Extraction

- Scheduled jobs to download data from Queensland Government APIs/data portals
- Support for CSV, GeoJSON, and other data formats
- Logging of data source version and timestamps

```javascript
// Node.js ETL script example
const { downloadFile } = require('./utils/download');
const { parseCSV } = require('./utils/parser');
const { Pool } = require('pg');
const fs = require('fs');
const path = require('path');
const logger = require('./utils/logger');

// Database connection
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});

// Queensland crash data URL
const CRASH_DATA_URL = 'https://www.data.qld.gov.au/dataset/f3e0ca94-2d7b-44ee-abef-d6b06e9b0729/resource/e88943c0-5968-4972-a15f-38e120d72ec0/download/_1_crash_locations.csv';
const DOWNLOAD_DIR = path.join(__dirname, 'downloads');

async function extractCrashData() {
  try {
    logger.info('Starting crash data extraction');
    
    // Create download directory if it doesn't exist
    if (!fs.existsSync(DOWNLOAD_DIR)) {
      fs.mkdirSync(DOWNLOAD_DIR, { recursive: true });
    }
    
    // Download the file
    const filePath = path.join(DOWNLOAD_DIR, '_1_crash_locations.csv');
    await downloadFile(CRASH_DATA_URL, filePath);
    
    // Read and parse the CSV file
    const csvData = fs.readFileSync(filePath, 'utf8');
    const parsedData = await parseCSV(csvData);
    
    logger.info(`Extracted ${parsedData.length} crash records`);
    
    return parsedData;
  } catch (error) {
    logger.error('Error extracting crash data:', error);
    throw error;
  }
}

module.exports = { extractCrashData };
```

#### 2. Transformation

- Data cleaning and normalization
- Coordinate transformation and validation
- Enrichment with additional data (e.g., road information)
- Handling of missing or invalid values

```javascript
// Node.js transformation example
const { isValid } = require('./utils/validation');
const logger = require('./utils/logger');

function transformCrashData(rawData) {
  try {
    logger.info('Starting crash data transformation');
    
    const transformedData = rawData
      .filter(record => {
        // Filter out records with invalid coordinates
        const lat = parseFloat(record.LATITUDE || record.Latitude || record.latitude);
        const lng = parseFloat(record.LONGITUDE || record.Longitude || record.longitude);
        
        return !isNaN(lat) && !isNaN(lng) && isValid(lat, lng);
      })
      .map((record, index) => {
        // Extract and normalize data
        const latitude = parseFloat(record.LATITUDE || record.Latitude || record.latitude);
        const longitude = parseFloat(record.LONGITUDE || record.Longitude || record.longitude);
        
        // Map severity
        let severity = 'Minor';
        const severityField = record.CRASH_SEVERITY || record.Crash_Severity || record.SEVERITY || record.Severity;
        
        if (severityField) {
          if (String(severityField).includes('Fatal')) {
            severity = 'Fatal';
          } else if (String(severityField).includes('Hospital') || String(severityField).includes('Serious')) {
            severity = 'Serious';
          }
        }
        
        // Extract date information
        const dateField = record.CRASH_DATE || record.Crash_Date || record.DATE || record.Date;
        let crashDate = null;
        
        try {
          crashDate = new Date(dateField).toISOString();
        } catch (error) {
          crashDate = new Date().toISOString();
          logger.warn(`Invalid date format for record ${index}, using current date`);
        }
        
        // Create transformed record
        return {
          id: `crash_${index}_${Date.now()}`,
          latitude,
          longitude,
          geom: `POINT(${longitude} ${latitude})`,
          crash_date: crashDate,
          severity,
          road_name: record.CRASH_ROAD_NAME || record.Road_Name || record.ROAD || record.Road || 'Unknown',
          casualties: parseInt(record.CASUALTY_COUNT || record.Casualty_Count || record.CASUALTIES || record.Casualties || 0),
          vehicles: parseInt(record.VEHICLE_COUNT || record.Vehicle_Count || record.VEHICLES || record.Vehicles || 0),
          crash_nature: record.CRASH_NATURE || record.Crash_Nature || record.NATURE || record.Nature || 'Unknown',
          road_feature: record.CRASH_ROAD_FEATURE || record.Road_Feature || 'Unknown',
          speed_limit: parseInt(record.SPEED_LIMIT || record.Speed_Limit || record.SPEED || record.Speed || 0),
          weather_condition: record.CRASH_ATMOSPHERIC_CONDITION || record.Weather_Condition || 'Unknown',
          lighting_condition: record.CRASH_LIGHTING_CONDITION || record.Lighting_Condition || 'Unknown',
          road_surface: record.CRASH_ROAD_SURFACE_CONDITION || record.Road_Surface || 'Unknown',
          is_preliminary: (new Date(crashDate) > new Date(Date.now() - 365 * 24 * 60 * 60 * 1000))
        };
      });
    
    logger.info(`Transformed ${transformedData.length} crash records`);
    
    return transformedData;
  } catch (error) {
    logger.error('Error transforming crash data:', error);
    throw error;
  }
}

module.exports = { transformCrashData };
```

#### 3. Loading

- Batch insert/update to PostgreSQL database
- Transaction management for data consistency
- Validation of referential integrity
- Error handling and retries

```javascript
// Node.js database loading example
const { Pool } = require('pg');
const format = require('pg-format');
const logger = require('./utils/logger');

// Database connection
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});

async function loadCrashData(transformedData) {
  const client = await pool.connect();
  
  try {
    logger.info('Starting crash data loading to database');
    
    // Begin transaction
    await client.query('BEGIN');
    
    // Prepare data for bulk insert
    const values = transformedData.map(record => [
      record.id,
      record.latitude,
      record.longitude,
      record.geom,
      record.crash_date,
      record.severity,
      record.road_name,
      record.casualties,
      record.vehicles,
      record.crash_nature,
      record.road_feature,
      record.speed_limit,
      record.weather_condition,
      record.lighting_condition,
      record.road_surface,
      record.is_preliminary
    ]);
    
    // Bulk insert
    const query = format(
      `INSERT INTO crashes (
        id, latitude, longitude, geom, crash_date, severity, 
        road_name, casualties, vehicles, crash_nature, 
        road_feature, speed_limit, weather_condition, 
        lighting_condition, road_surface, is_preliminary
      ) 
      VALUES %L 
      ON CONFLICT (id) DO UPDATE SET 
        latitude = EXCLUDED.latitude,
        longitude = EXCLUDED.longitude,
        geom = EXCLUDED.geom,
        crash_date = EXCLUDED.crash_date,
        severity = EXCLUDED.severity,
        road_name = EXCLUDED.road_name,
        casualties = EXCLUDED.casualties,
        vehicles = EXCLUDED.vehicles,
        crash_nature = EXCLUDED.crash_nature,
        road_feature = EXCLUDED.road_feature,
        speed_limit = EXCLUDED.speed_limit,
        weather_condition = EXCLUDED.weather_condition,
        lighting_condition = EXCLUDED.lighting_condition,
        road_surface = EXCLUDED.road_surface,
        is_preliminary = EXCLUDED.is_preliminary,
        updated_at = CURRENT_TIMESTAMP
      `,
      values
    );
    
    await client.query(query);
    
    // Commit transaction
    await client.query('COMMIT');
    
    logger.info(`Successfully loaded ${transformedData.length} crash records`);
  } catch (error) {
    // Rollback transaction on error
    await client.query('ROLLBACK');
    logger.error('Error loading crash data to database:', error);
    throw error;
  } finally {
    // Release client back to pool
    client.release();
  }
}

module.exports = { loadCrashData };
```

### Data Update Strategy

- Daily updates for traffic incidents (real-time where possible)
- Monthly updates for crash data
- Annual updates for AADT data
- Clearly mark preliminary data (crashes less than 12 months old)
- Maintain data versioning and history

### Error Handling Strategies

1. **API Error Handling**:
   - Consistent error response format
   - Appropriate HTTP status codes
   - Detailed error messages (in development, sanitized in production)
   - Rate limiting with exponential backoff for retries

```javascript
// API Error Handler Middleware
const errorHandler = (err, req, res, next) => {
  const status = err.statusCode || 500;
  const message = process.env.NODE_ENV === 'production' && status === 500 
    ? 'Internal Server Error' 
    : err.message;
  
  // Log the error
  logger.error(`${status} - ${message} - ${req.originalUrl} - ${req.method} - ${req.ip}`);
  
  // Structured error response
  res.status(status).json({
    error: {
      status,
      message,
      timestamp: new Date().toISOString(),
      path: req.originalUrl,
      code: err.code || 'INTERNAL_ERROR'
    }
  });
};
```

2. **ETL Error Handling**:
   - Retry mechanisms for network failures
   - Partial success handling (continue processing valid records)
   - Data validation checkpoints
   - Alert notifications for critical failures

3. **Database Error Handling**:
   - Transaction rollbacks to maintain data integrity
   - Connection pool management
   - Deadlock detection and retry logic
   - Backup strategies and disaster recovery procedures

## 6. AI Integration for Report Generation

The road.engineering platform will leverage AI models for generating insightful crash data reports. This integration will enhance the value of the generated reports by providing natural language summaries, pattern detection, and recommendations.

### Report Generation Flow

```
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
│ Collect Road Data │     │ Process & Analyze │     │  Generate Report  │
│                   │────►│       Data        │────►│  with AI Insights │
└───────────────────┘     └───────────────────┘     └───────────────────┘
        ▲                          │                          │
        │                          ▼                          ▼
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
│   Data Sources    │     │  Visualization    │     │  PDF/CSV Output   │
│   - Crash Data    │     │   Generation      │     │     with Charts   │
│   - AADT Data     │     │                   │     │                   │
└───────────────────┘     └───────────────────┘     └───────────────────┘
```

### Claude API Integration

```javascript
// AI Report Summary Generation
const { Claude } = require('@anthropic/sdk');
const claude = new Claude({
  apiKey: process.env.CLAUDE_API_KEY,
});

async function generateReportSummary(roadName, crashData, aadtData) {
  try {
    const prompt = `
    Generate a comprehensive road safety report summary for ${roadName} based on the following crash and traffic data. 
    Include patterns, trends, potential causes of crashes, and safety recommendations.
    
    CRASH DATA:
    ${JSON.stringify(crashData, null, 2)}
    
    TRAFFIC DATA:
    ${JSON.stringify(aadtData, null, 2)}
    
    The summary should include:
    1. Key statistics overview
    2. Temporal analysis (time of day, day of week, seasonal patterns)
    3. Crash type patterns
    4. Severity analysis
    5. Contributing factors
    6. Comparison to similar roads
    7. Safety recommendations
    `;
    
    const response = await claude.messages.create({
      model: "claude-3-sonnet-20240229",
      max_tokens: 1024,
      temperature: 0.1,
      system: "You are an expert road safety analyst with expertise in analyzing crash data and providing insights.",
      messages: [
        {
          role: "user",
          content: prompt
        }
      ]
    });
    
    return response.content;
  } catch (error) {
    logger.error('Error generating AI report summary:', error);
    return 'Unable to generate AI summary at this time. Please refer to the statistical data in the report.';
  }
}
```

### AI-Enhanced Pattern Recognition

```javascript
// AI-Enhanced Crash Pattern Recognition
async function identifyCrashPatterns(crashData) {
  try {
    const prompt = `
    Analyze the following crash data and identify any significant patterns, clusters, or correlations.
    Focus on identifying potential contributing factors and common crash scenarios.
    
    CRASH DATA:
    ${JSON.stringify(crashData, null, 2)}
    
    For each pattern you identify, provide:
    1. Pattern description
    2. Key contributing factors
    3. Statistical significance
    4. Potential countermeasures
    `;
    
    const response = await claude.messages.create({
      model: "claude-3-sonnet-20240229",
      max_tokens: 1024,
      temperature: 0.2,
      system: "You are an expert road safety analyst specializing in crash pattern recognition and countermeasure development.",
      messages: [
        {
          role: "user",
          content: prompt
        }
      ]
    });
    
    // Parse and structure the AI response
    const patternAnalysis = JSON.parse(response.content[0].text);
    
    return patternAnalysis;
  } catch (error) {
    logger.error('Error identifying crash patterns:', error);
    return [];
  }
}
```

## 7. Scalability Considerations

### Caching Strategy

1. **Redis Caching**:
   - Cache frequently accessed data (roads, AADT)
   - Store pre-computed aggregations and statistics
   - Cache map tile data for common zoom levels
   - Implement time-based cache invalidation

```javascript
// Redis Caching Example
const Redis = require('ioredis');
const redis = new Redis(process.env.REDIS_URL);

// Cache middleware
const cacheMiddleware = (ttl = 3600) => {
  return async (req, res, next) => {
    try {
      // Create a unique cache key based on the request
      const cacheKey = `api:${req.originalUrl}`;
      
      // Check if we have a cached response
      const cachedResponse = await redis.get(cacheKey);
      
      if (cachedResponse) {
        // Send cached response
        res.setHeader('X-Cache', 'HIT');
        return res.json(JSON.parse(cachedResponse));
      }
      
      // Store the original res.json method
      const originalJson = res.json;
      
      // Override res.json method to cache the response
      res.json = function(data) {
        // Cache the response (only if status is 200)
        if (res.statusCode === 200) {
          redis.set(cacheKey, JSON.stringify(data), 'EX', ttl);
        }
        
        // Call the original method
        return originalJson.call(this, data);
      };
      
      res.setHeader('X-Cache', 'MISS');
      next();
    } catch (error) {
      // On error, continue without caching
      next();
    }
  };
};
```

2. **Spatial Data Caching**:
   - Generate and cache clustered marker data for different zoom levels
   - Pre-compute and cache hotspot analysis results
   - Cache frequently accessed geospatial queries

### Load Balancing and Scaling

1. **Horizontal Scaling**:
   - Deploy multiple API instances behind a load balancer
   - Scale services independently based on demand
   - Use containerization (Docker) for consistent deployments

2. **Database Scaling**:
   - Read replicas for handling high query loads
   - Consider PostgreSQL partitioning for large datasets
   - Implement connection pooling

3. **Serverless Functions**:
   - Use serverless functions for report generation
   - Scale automatically with demand
   - Isolate resource-intensive operations

## 8. Security Considerations

### Authentication and Authorization

1. **JWT-Based Authentication**:
   - Issue signed JWTs for authenticated sessions
   - Include appropriate claims and expiration
   - Implement token refresh mechanism

2. **Role-Based Access Control**:
   - Define roles (e.g., public, analyst, admin)
   - Implement middleware to enforce role-based permissions
   - Fine-grained control over sensitive operations

### API Security

1. **API Gateway Security**:
   - Rate limiting to prevent abuse
   - Request validation and sanitization
   - CORS configuration for web clients

2. **Data Protection**:
   - Encryption of sensitive data
   - Anonymization of personal information in crash data
   - Secure handling of report files

This backend structure document provides a comprehensive guide for implementing the server-side components of the road.engineering platform. By following these guidelines, the development team can create a robust, scalable, and secure backend that effectively handles Queensland's road safety data and provides valuable insights to users.
