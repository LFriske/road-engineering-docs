# App Flow Document - road.engineering

This document outlines the step-by-step user journey through the road.engineering platform, detailing how users interact with the application, the flow of data, and key decision points. It serves as a guide for both developers and stakeholders to understand the complete user experience and the underlying processes that support it.

## 1. User Journey Overview

The road.engineering application follows a progressive disclosure approach, allowing users to start with a high-level view of Queensland's road safety data and then drill down into specific areas or roads of interest. The overall flow consists of these major stages:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Initial Map   │     │  Layer Selection│     │  Road Selection │     │  Report         │
│      View       │────►│  & Filtering    │────►│  & Analysis     │────►│  Generation     │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
```

## 2. Detailed User Flow

### 2.1 Initial Landing & Map View

1. User visits road.engineering website
2. The application loads with the following initial state:
   - Map centered on Queensland, Australia (Brisbane area)
   - Default zoom level set to show regional context
   - Crash data layer activated by default
   - Most recent year of data displayed initially

```
┌───────────────────────────────────────────────────────────────────┐
│ ┌────────────────────────────────────────────────────────────┐    │
│ │ road.engineering                                [User Menu] │    │
│ └────────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌────────────┐ ┌───────────┐ ┌────────────┐                      │
│ │ Crash Data │ │ Traffic   │ │ AADT Data  │                      │
│ └────────────┘ └───────────┘ └────────────┘                      │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐    │
│ │                                                           │    │
│ │                                                           │    │
│ │                                                           │    │
│ │                      Interactive Map                      │    │
│ │                  (Showing Crash Points)                   │    │
│ │                                                           │    │
│ │                                                           │    │
│ │                                                           │    │
│ └───────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐    │
│ │ Map Legend                                                │    │
│ │ ● Fatal Crash  ● Serious Crash  ● Minor Crash            │    │
│ └───────────────────────────────────────────────────────────┘    │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

**Backend Processes:**
- Load and display map using Leaflet.js
- Fetch initial crash data for visible bounds
- Initialize layer management system
- Set up event listeners for map interactions

**Data Flow:**
1. Frontend sends request for crash data within current map bounds
2. Backend queries crash data from PostgreSQL/PostGIS
3. Data is transformed into geospatial format for map rendering
4. Crashes are displayed as markers on the map with appropriate styling based on severity

**AI Assistance Opportunities:**
- Automated identification of crash clusters for initial view
- Smart default zoom level based on data density
- Pre-loading prediction for commonly viewed areas

### 2.2 Layer Selection & Filtering

1. User toggles between different data layers:
   - Crash Data (traffic accidents)
   - Live Traffic (current incidents)
   - AADT Data (traffic volume)
   
2. User applies filters to refine the displayed data:
   - Date range selection
   - Crash severity filters
   - Road type filters
   - Additional attributes (weather, time of day, vehicle types)

```
┌───────────────────────────────────────────────────────────────────┐
│ ┌────────────────────────────────────────────────────────────┐    │
│ │ road.engineering                                [User Menu] │    │
│ └────────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌────────────┐ ┌───────────┐ ┌────────────┐                      │
│ │[Crash Data]│ │ Traffic   │ │ AADT Data  │                      │
│ └────────────┘ └───────────┘ └────────────┘                      │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐    │
│ │ Filters:                                                  │    │
│ │ ┌─────────────────┐ ┌────────────────┐ ┌───────────────┐ │    │
│ │ │ Date: 2018-2023 │ │ Severity: All  ▼│ │ Road Type: All▼│ │    │
│ │ └─────────────────┘ └────────────────┘ └───────────────┘ │    │
│ │ ┌─────────────────────────────────────────────────────┐  │    │
│ │ │ Apply Filters                                       │  │    │
│ │ └─────────────────────────────────────────────────────┘  │    │
│ └───────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐    │
│ │                                                           │    │
│ │                      Interactive Map                      │    │
│ │            (Showing Filtered Crash Points)               │    │
│ │                                                           │    │
│ └───────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐    │
│ │ Map Legend                                                │    │
│ │ ● Fatal Crash  ● Serious Crash  ● Minor Crash            │    │
│ └───────────────────────────────────────────────────────────┘    │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

**Backend Processes:**
- Process filter parameters to construct appropriate database queries
- Fetch filtered data from appropriate services (crash, traffic, AADT)
- Apply layer-specific styling and clustering
- Cache common filter combinations for performance

**Data Flow:**
1. User sets filter parameters in the UI
2. Frontend sends updated request with filter parameters
3. Backend applies filters to database queries
4. Filtered results are returned and transformed for map display
5. Map updates with filtered markers, adjusting clustering as needed

**AI Assistance Opportunities:**
- Suggest relevant filters based on current view and data patterns
- Identify unusual crash patterns in current view
- Predict user intent based on filter combinations and suggest additional filters

### 2.3 Road Selection & Analysis

1. User selects a specific road or segment for detailed analysis through:
   - Direct map interaction (clicking on road)
   - Search functionality (entering road name/ID)
   - Predefined regions or hotspots
   
2. The application displays detailed statistics and visualizations for the selected road:
   - Crash frequency and severity
   - Temporal patterns (time of day, day of week, seasonal)
   - Comparison to similar roads
   - Hotspot identification within the selected road

```
┌───────────────────────────────────────────────────────────────────┐
│ ┌────────────────────────────────────────────────────────────┐    │
│ │ road.engineering                                [User Menu] │    │
│ └────────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌────────────────────────────────┐ ┌─────────────────────────┐   │
│ │                                │ │ Road Analysis: M1 Bruce  │   │
│ │                                │ │ Highway                  │   │
│ │                                │ │                          │   │
│ │                                │ │ Total Crashes: 1,245     │   │
│ │                                │ │ - Fatal: 28              │   │
│ │                                │ │ - Serious: 387           │   │
│ │         Interactive Map        │ │ - Minor: 830             │   │
│ │      (Selected Road View)      │ │                          │   │
│ │                                │ │ ┌─────────────────────┐  │   │
│ │                                │ │ │ Crash Type Chart    │  │   │
│ │                                │ │ └─────────────────────┘  │   │
│ │                                │ │                          │   │
│ │                                │ │ ┌─────────────────────┐  │   │
│ │                                │ │ │ Time of Day Chart   │  │   │
│ └────────────────────────────────┘ │ └─────────────────────┘  │   │
│                                     │                          │   │
│                                     │ ┌─────────────────────┐  │   │
│                                     │ │ Generate Full Report│  │   │
│                                     │ └─────────────────────┘  │   │
│                                     └─────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
```

**Backend Processes:**
- Fetch comprehensive road data and related crashes
- Calculate statistical summaries and aggregations
- Generate data visualizations (charts, heatmaps)
- Identify and highlight significant patterns or hotspots

**Data Flow:**
1. User selects a road or segment
2. Frontend requests detailed road data and associated crashes
3. Backend retrieves road geometry and all crashes along the selected road
4. Backend computes analytics (crash rates, patterns, comparisons)
5. Data is returned to frontend for visualization and interactive analysis
6. Map centers and zooms to focus on the selected road

**AI Assistance Opportunities:**
- Automatically identify and highlight crash patterns specific to the selected road
- Generate natural language insights about the road's safety profile
- Compare selected road to similar roads and identify anomalies
- Suggest potential contributing factors based on crash patterns

### 2.4 Report Generation

1. User requests a detailed report for the selected road by:
   - Clicking "Generate Report" button
   - Customizing report parameters (date range, detail level, format)
   
2. The application generates and delivers a comprehensive report:
   - Statistical summary of crash data
   - Visual charts and graphs
   - Maps highlighting hotspots
   - Temporal and spatial analysis
   - Optionally, AI-generated insights and recommendations

```
┌───────────────────────────────────────────────────────────────────┐
│ ┌────────────────────────────────────────────────────────────┐    │
│ │ road.engineering                                [User Menu] │    │
│ └────────────────────────────────────────────────────────────┘    │
│                                                                   │
│ ┌────────────────────────────────┐ ┌─────────────────────────┐   │
│ │                                │ │ Report Options          │   │
│ │                                │ │                          │   │
│ │                                │ │ Road: M1 Bruce Highway   │   │
│ │                                │ │                          │   │
│ │                                │ │ Date Range:              │   │
│ │         Interactive Map        │ │ [01/01/2018]-[31/12/2023]│   │
│ │      (Selected Road View)      │ │                          │   │
│ │                                │ │ Include:                 │   │
│ │                                │ │ ☑ Crash Details          │   │
│ │                                │ │ ☑ Statistical Charts     │   │
│ │                                │ │ ☑ Hotspot Maps           │   │
│ │                                │ │ ☑ AI-Generated Insights  │   │
│ │                                │ │                          │   │
│ └────────────────────────────────┘ │ Format:                  │   │
│                                     │ ○ PDF  ● CSV             │   │
│                                     │                          │   │
│                                     │ ┌─────────────────────┐  │   │
│                                     │ │     Generate Now    │  │   │
│                                     │ └─────────────────────┘  │   │
│                                     └─────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
```

**Backend Processes:**
- Process report generation request with parameters
- Compile comprehensive crash and road data
- Generate visualizations and statistical summaries
- Optionally invoke AI for pattern recognition and insights
- Create formatted output document (PDF, CSV)
- Store report for future access

**Data Flow:**
1. User submits report generation request with parameters
2. Frontend sends report request to backend
3. Backend initiates asynchronous report generation process
4. Backend queries all relevant data for the specified road and date range
5. Report generation service processes data and creates visualizations
6. If enabled, AI service analyzes data and generates insights
7. Report is assembled in requested format
8. User receives notification when report is ready
9. User downloads or views the completed report

**AI Assistance Opportunities:**
- Generate natural language summaries of crash patterns
- Identify potential contributing factors not obvious from statistics
- Compare crash patterns to similar roads to identify anomalies
- Suggest potential safety improvements based on crash types
- Predict future crash likelihood based on historical patterns

## 3. Alternative Flows

### 3.1 Starting with a Known Road

1. User knows which road they want to analyze
2. User enters road name or ID in search box
3. Application finds and highlights the road on the map
4. User proceeds directly to road analysis and report generation

### 3.2 Starting with Traffic Incidents

1. User is interested in current traffic conditions
2. User selects Traffic layer on initial view
3. Application displays current traffic incidents
4. User explores traffic patterns and then switches to historical crash data for areas of interest

### 3.3 Sharing & Collaboration

1. User generates a report for a specific road
2. User shares the report via a unique URL
3. Other users can access the report without regenerating it
4. Optionally, users with appropriate permissions can comment on or annotate the report

## 4. Mobile User Journey

The mobile experience follows the same core flow but adapts to smaller screens:

1. **Initial View**: Map takes priority with minimal UI controls
2. **Layer Selection**: Controls accessible via a collapsible panel
3. **Road Selection**: Touch interface with enhanced targeting for road selection
4. **Analysis View**: Vertical stacking of map and data visualizations
5. **Report Options**: Full-screen modal for report configuration

```
┌───────────────────────┐    ┌───────────────────────┐
│ road.engineering  ☰   │    │ road.engineering  ☰   │
│                       │    │                       │
│ [Crash][Traffic][AADT]│    │ ┌─────────────────┐   │
│                       │    │ │ Road: Bruce Hwy │   │
│                       │    │ └─────────────────┘   │
│                       │    │                       │
│     Interactive       │    │     Interactive       │
│         Map           │    │         Map           │
│                       │    │   (Selected Road)     │
│                       │    │                       │
│                       │    │                       │
│                       │    │ ──────────────────────│
│                       │    │ Road Analysis         │
│                       │    │ ┌────────────┐        │
│                       │    │ │ Chart 1    │        │
│                       │    │ └────────────┘        │
│ ┌───────────────────┐ │    │                       │
│ │    Map Legend     │ │    │ [Generate Report]     │
│ └───────────────────┘ │    │                       │
└───────────────────────┘    └───────────────────────┘
```

## 5. Key Integration Points for AI

The road.engineering platform incorporates AI at several key points to enhance user experience and provide deeper insights:

### 5.1 Natural Language Search

**Implementation**: Integrate Claude API to process natural language queries for road selection.

**Example Flow**:
1. User types: "Show me the most dangerous sections of Pacific Motorway"
2. AI processes the query to understand intent
3. System identifies Pacific Motorway and filters for high-crash segments
4. Map focuses on these areas with appropriate visualization

### 5.2 Pattern Recognition & Insight Generation

**Implementation**: Use Claude API to analyze crash patterns and generate insights.

**Example Flow**:
1. System collects crash data for selected road
2. AI analyzes patterns beyond basic statistics
3. AI identifies unusual patterns (e.g., "Rear-end crashes are 2.5x more common on this road than similar highways")
4. Insights are presented alongside statistical data in the analysis view

### 5.3 Predictive Analysis

**Implementation**: Use AI to predict potential future crash locations based on historical patterns.

**Example Flow**:
1. AI analyzes historical crash data, road features, and traffic volumes
2. System generates a risk heatmap showing predicted crash likelihood
3. High-risk areas are highlighted in the map view
4. User can toggle between historical data and predictive view

### 5.4 Report Summaries & Recommendations

**Implementation**: Claude API generates comprehensive written analysis for reports.

**Example Flow**:
1. User requests a report with AI insights enabled
2. System compiles all raw data and statistics
3. AI generates a written executive summary highlighting key findings
4. AI provides data-driven recommendations for potential safety improvements
5. These elements are incorporated into the final report

## 6. Data Flow Diagrams

### 6.1 High-Level Data Flow

```
┌────────────┐     ┌────────────┐     ┌────────────┐     ┌────────────┐
│            │     │            │     │            │     │            │
│   User     │◄───►│  Frontend  │◄───►│   API      │◄───►│  Database  │
│            │     │            │     │  Gateway   │     │            │
└────────────┘     └────────────┘     └────────────┘     └────────────┘
                                            │
                                            │
                                            ▼
                        ┌────────────┐     ┌────────────┐
                        │            │     │            │
                        │   Cache    │◄───►│ AI Services│
                        │            │     │            │
                        └────────────┘     └────────────┘
```

### 6.2 Report Generation Data Flow

```
┌────────────┐     ┌────────────┐     ┌────────────┐
│            │     │            │     │            │
│   User     │────►│  Report    │────►│   Data     │
│  Request   │     │  Service   │     │ Retrieval  │
│            │     │            │     │            │
└────────────┘     └────────────┘     └────────────┘
                         │                  │
                         │                  ▼
                         │           ┌────────────┐
                         │           │            │
                         │           │ Statistical│
                         │           │  Analysis  │
                         │           │            │
                         │           └────────────┘
                         │                  │
                         ▼                  ▼
                   ┌────────────┐    ┌────────────┐
                   │            │    │            │
                   │    AI      │◄───│ Chart & Map│
                   │  Insights  │    │ Generation │
                   │            │    │            │
                   └────────────┘    └────────────┘
                         │                  │
                         ▼                  ▼
                         ┌────────────────────┐
                         │                    │
                         │  Report Assembly   │
                         │                    │
                         └────────────────────┘
                                   │
                                   ▼
                         ┌────────────────────┐
                         │                    │
                         │  Report Delivery   │
                         │                    │
                         └────────────────────┘
```

## 7. Key Performance Indicators

To measure the effectiveness of the user journey and app flow, the following KPIs will be tracked:

1. **User Engagement**:
   - Average session duration
   - Number of interactions per session
   - Layer toggle frequency
   - Road selection rate

2. **Task Completion**:
   - Report generation rate
   - Search success rate
   - Filter usage patterns
   - Feature discovery metrics

3. **Performance**:
   - Time to initial map load
   - Map rendering performance
   - API response times for different data layers
   - Report generation time

4. **User Satisfaction**:
   - User feedback ratings
   - Feature usage distribution
   - Return user rate
   - Sharing/export actions

## 8. Future Flow Enhancements

The following enhancements are planned for future iterations:

1. **Personalized Dashboards**:
   - Allow users to save favorite roads
   - Create custom views with preferred metrics
   - Set up notifications for road condition changes

2. **Comparative Analysis**:
   - Enable side-by-side comparison of multiple roads
   - Historical trend comparison across different time periods
   - Benchmark roads against regional or road-type averages

3. **Advanced AI Interaction**:
   - Natural language interface for complex queries
   - AI-guided exploration of unusual patterns
   - Predictive modeling for future crash risk

4. **Mobile-Optimized Flows**:
   - Location-aware interface showing nearby data
   - Simplified reporting for field use
   - Voice-guided navigation through complex data

This app flow document provides a comprehensive overview of the user journey through the road.engineering platform, highlighting key interactions, data flows, and AI integration points. It serves as a roadmap for development and a reference for understanding the complete user experience.
