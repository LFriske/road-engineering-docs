# 50-Step Implementation Plan - road.engineering

This document provides a detailed, step-by-step plan for building the road.engineering project using AI-assisted coding tools. Each step is actionable and includes specific prompts for AI models to help accelerate development and improve code quality.

## Phase 1: Project Setup and Foundation (Days 1-7)

### Day 1: Project Planning and Setup

#### Step 1: Create Project Repository and Documentation
**Action:** Set up GitHub repository and add initial documentation.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Generate a comprehensive README.md file for a geospatial web application called road.engineering. 
The application will visualize Queensland road crash data and AADT data on an interactive map, 
allowing users to select roads and generate historical crash reports.
Include sections for project overview, tech stack, setup instructions, and data sources.
```

#### Step 2: Initialize Frontend Project Structure
**Action:** Create React application with TypeScript.
**AI Tool:** Claude in Cursor
**Prompt:**
```
I want to create a new React TypeScript project for a map-based web application. 
Please help me set up the initial structure with Vite instead of Create React App.
Include proper TypeScript configurations and folder structure for components, hooks, services, types, and utilities.
Also set up ESLint, Prettier, and proper .gitignore.
```

#### Step 3: Install Base Dependencies
**Action:** Add core dependencies for the project.
**AI Tool:** GitHub Copilot
**Prompt:**
```
I need a complete package.json file for a geospatial web application with the following requirements:
- React with TypeScript
- Leaflet.js for map visualization
- Tailwind CSS for styling
- React Router for navigation
- PapaParse for CSV parsing
- Recharts for data visualization
- Axios for API requests
Please include all dependencies with their latest stable versions.
```

#### Step 4: Configure Tailwind CSS
**Action:** Set up Tailwind CSS with custom configuration.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a tailwind.config.js file with a custom color palette suitable for a road safety visualization application.
I need colors for:
- Fatal crashes (red tones)
- Serious crashes (orange/amber tones)
- Minor crashes (yellow tones)
- Brand colors (blue theme)
Also extend the configuration for custom map-related elements and responsive breakpoints.
```

### Day 2: Map Component Foundation

#### Step 5: Create Basic Map Component
**Action:** Implement initial Leaflet map component.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Generate a React component using TypeScript and Leaflet.js that displays an interactive map centered on Queensland, Australia.
The component should:
- Initialize with Brisbane coordinates (-27.4698, 153.0251)
- Have proper zoom controls
- Support responsive sizing
- Include TypeScript interfaces for props and state
- Handle cleanup to prevent memory leaks
```

#### Step 6: Add Map Layer Controls
**Action:** Create controls for toggling between map layers.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create a LayerControl React component that:
- Allows users to toggle between three layers: "crash", "traffic", and "aadt"
- Uses Tailwind CSS for styling
- Shows active layer with visual distinction
- Is accessible with proper ARIA attributes
- Communicates selected layer to parent component
```

#### Step 7: Implement Map State Management
**Action:** Set up state management for map view and layers.
**AI Tool:** Claude in Cursor
**Prompt:**
```
I need to create custom hooks to manage map state in a React application with TypeScript.
Please create:
1. A useMapState hook that tracks viewport, zoom level, and bounds
2. A useLayerState hook that manages active layers and their visibility
Make sure these hooks are reusable, have proper TypeScript types, and follow React best practices.
```

### Day 3: Data Services Setup

#### Step 8: Create Data Service Interfaces
**Action:** Define interfaces for data services.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Generate TypeScript interfaces for Queensland road crash data and AADT data. 
The interfaces should:
- Reflect the structure of the data from the Queensland Government datasets
- Include proper types for all fields
- Have documentation comments explaining each property
- Include geospatial properties (latitude, longitude)
- Account for potential null/undefined values
```

#### Step 9: Implement CSV Parsing Utility
**Action:** Create utility for parsing CSV data.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a utility module using TypeScript and PapaParse to process CSV files from the Queensland Government's crash data portal.
The utility should:
- Handle asynchronous file loading
- Support both local files and remote URLs
- Include error handling for malformed data
- Transform column names to consistent camelCase
- Convert GPS coordinates to the format needed by Leaflet
```

#### Step 10: Set Up API Service Structure
**Action:** Create API service for data fetching.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create an API service module using Axios and TypeScript that will fetch data from the Queensland Government's data portals.
Include functions for:
- Fetching crash data with filtering options
- Fetching AADT data with filtering options
- Fetching traffic incident data
Implement proper error handling, request caching, and TypeScript types.
```

### Day 4: Data Visualization Components

#### Step 11: Create Crash Marker Component
**Action:** Implement component for displaying crash markers on the map.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a React component that renders crash data points on a Leaflet map. The component should:
- Display markers with colors based on crash severity (fatal, serious, minor)
- Include popups with crash details when markers are clicked
- Handle marker clustering for better performance with large datasets
- Manage marker creation and cleanup efficiently
- Accept a filtered dataset as a prop
```

#### Step 12: Implement AADT Visualization Layer
**Action:** Create AADT data visualization.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
I need to visualize Annual Average Daily Traffic (AADT) data on a map using Leaflet and React.
Create a component that:
- Represents traffic volume using circle size or color intensity
- Includes a legend that explains the visualization
- Provides tooltips showing the actual AADT value when hovering
- Handles data updates efficiently
```

#### Step 13: Create Map Legend Component
**Action:** Implement legend for map data.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create a React component for a map legend that:
- Shows the meaning of colors and symbols used on the map
- Updates dynamically based on which layer is active
- Uses Tailwind CSS for styling
- Is positioned in the bottom-left corner of the map
- Is collapsible on mobile devices
```

### Day 5: Data Filtering and Controls

#### Step 14: Implement Date Range Selector
**Action:** Create date range filter component.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a React component for selecting a date range to filter crash data.
The component should:
- Allow selection of start and end dates
- Validate date ranges
- Have a clean UI using Tailwind CSS
- Support preset ranges (last year, last 5 years, etc.)
- Emit events when the range changes
```

#### Step 15: Create Severity Filter Component
**Action:** Implement crash severity filter.
**AI Tool:** Gemini Flash
**Prompt:**
```
Generate a React component for filtering crash data by severity levels.
It should:
- Allow selecting multiple severity levels (Fatal, Serious, Minor)
- Use checkboxes with appropriate styling
- Show counts of each severity type if available
- Be accessible with proper ARIA attributes
- Use Tailwind CSS for styling
```

#### Step 16: Implement Road Type Filter
**Action:** Create road type filter component.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a dropdown filter component for selecting road types in a traffic safety application.
The component should:
- Allow filtering by road types (Highway, Arterial, Local, etc.)
- Use a multi-select dropdown pattern
- Be built with accessibility in mind
- Use Tailwind CSS for styling
- Include TypeScript types and interfaces
```

### Day 6: Road Selection Interface

#### Step 17: Create Road Search Component
**Action:** Implement road search functionality.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a road search component that allows users to find Queensland roads by name or ID.
Features should include:
- Autocomplete suggestions as user types
- Handling of partial matches
- Clear indication of selected road
- Proper keyboard navigation support
- Clean UI with Tailwind CSS
```

#### Step 18: Implement Map-Based Road Selection
**Action:** Add ability to select roads by clicking on map.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Implement a feature that allows users to select a road by clicking on it in the Leaflet map:
- Highlight the road when hovered over
- Select the road when clicked
- Show visual feedback for the selected road
- Manage the state of the currently selected road
- Ensure proper cleanup to prevent memory leaks
```

#### Step 19: Create Road Details Panel
**Action:** Implement panel to display information about selected road.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a React component that displays details about a selected road, including:
- Road name, ID, and type
- Length and location information
- Summary statistics about crashes (total count, by severity)
- Basic traffic volume information
The panel should be collapsible, use Tailwind CSS, and update whenever a new road is selected.
```

### Day 7: Initial Integration and Testing

#### Step 20: Integrate Components
**Action:** Assemble components into cohesive UI.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Help me integrate the map, layer controls, filters, and road selection components into a cohesive layout.
Create a main App component that:
- Has a responsive layout with sidebar that collapses on mobile
- Manages shared state between components
- Handles component communication
- Has proper loading states
- Uses Tailwind CSS for styling
```

#### Step 21: Implement Data Loading States
**Action:** Add loading indicators and error handling.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create a LoadingState component system for data fetching operations that:
- Shows appropriate loading spinners
- Displays helpful error messages
- Supports retry functionality
- Provides different visual states (loading, partial data, error, empty)
- Is reusable across the application
```

#### Step 22: Set Up Initial Testing
**Action:** Create basic tests for components.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a suite of Jest tests for the map component and data handling utilities.
Include:
- Unit tests for utility functions
- Component rendering tests
- Mock data for testing
- Test for error handling scenarios
- Basic integration tests for main flows
```

## Phase 2: Data Processing and Backend Setup (Days 8-14)

### Day 8: Backend Foundation

#### Step 23: Set Up Backend Project Structure
**Action:** Create Node.js backend with Express.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a Node.js backend project with Express and TypeScript that will serve as the API for the road.engineering frontend.
Include:
- Proper project structure
- TypeScript configuration
- Express setup with middleware
- Environment configuration
- API route structure
- Error handling middleware
```

#### Step 24: Configure Database
**Action:** Set up PostgreSQL with PostGIS.
**AI Tool:** Gemini Flash
**Prompt:**
```
Generate scripts to set up a PostgreSQL database with PostGIS extension for storing road crash and AADT data.
Include:
- Database schema creation SQL
- Table definitions for crashes, roads, and AADT data
- Spatial indexes for geometric data
- User permissions setup
- Connection configuration for Node.js
```

#### Step 25: Create Data Models
**Action:** Implement database models.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create TypeScript models/interfaces for the database entities (crashes, roads, AADT data) using TypeORM or a similar ORM.
Include:
- Entity definitions
- Relationships between entities
- Validation decorators
- Type-safe query methods
- Documentation for each model
```

### Day 9: ETL Pipeline Setup

#### Step 26: Create Data Downloader
**Action:** Implement service to download data from Queensland Government.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a Node.js script that will download the latest crash and AADT data from the Queensland Government data portal.
The script should:
- Handle authentication if needed
- Download data files (CSV or other formats)
- Verify data integrity
- Log the process
- Support scheduled execution
```

#### Step 27: Implement Data Transformation
**Action:** Create data processing pipeline.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create a data transformation pipeline for Queensland crash data that:
- Normalizes column names
- Validates data integrity
- Converts coordinates to appropriate format
- Enriches data with additional information
- Handles different CSV formats that might change over time
```

#### Step 28: Create Database Import Process
**Action:** Build database import functionality.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a Node.js script to import processed crash and AADT data into the PostgreSQL database.
The script should:
- Use transactions for data integrity
- Handle batch inserts for performance
- Update existing records instead of duplicating
- Verify data after import
- Generate a summary report of the import process
```

### Day 10: API Endpoints Development

#### Step 29: Implement Crash Data Endpoints
**Action:** Create API for crash data.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create Express API endpoints for crash data:
- GET /api/crashes with filtering options (bounds, road, date, severity)
- GET /api/crashes/:id for individual crash details
- Include pagination support
- Add proper error handling
- Implement query parameter validation
- Document with JSDoc or Swagger
```

#### Step 30: Implement AADT Data Endpoints
**Action:** Create API for AADT data.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create Express API endpoints for AADT (Annual Average Daily Traffic) data:
- GET /api/aadt with filtering (bounds, road, year)
- Include data aggregation options
- Support spatial queries
- Add proper error handling
- Document with JSDoc or Swagger
```

#### Step 31: Create Road Data Endpoints
**Action:** Implement API for road information.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create Express API endpoints for road data:
- GET /api/roads with search and filter options
- GET /api/roads/:id for specific road details
- Endpoint for road geometry data
- Include crash statistics in road details
- Support for spatial queries (roads within bounds)
- Documentation with JSDoc or Swagger
```

### Day 11: Report Generation Backend

#### Step 32: Create Report Data Service
**Action:** Implement service to compile report data.
**AI Tool:** Gemini Flash
**Prompt:**
```
Create a service that compiles comprehensive crash and AADT data for a given road to be used in report generation.
It should:
- Fetch all relevant data for a specific road
- Calculate statistics and aggregations
- Identify trends and patterns
- Structure data for both PDF and CSV formats
- Include spatial analysis of crash locations
```

#### Step 33: Implement PDF Report Generator
**Action:** Create PDF report generation service.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a Node.js service that generates PDF reports for road crash data using a library like PDFKit or jsPDF.
Features should include:
- Clean, professional layout
- Sections for statistics, trends, and details
- Charts and graphs for data visualization
- Map images showing crash locations
- Proper styling and branding
- Table of contents
```

#### Step 34: Create CSV Export Service
**Action:** Implement CSV data export.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create a service to export road crash data to CSV format with the following features:
- Comprehensive data inclusion
- Proper header formatting
- Data transformation for readability
- Support for different data subsets
- Streaming for large datasets
- Proper error handling
```

### Day 12: API Integration with Frontend

#### Step 35: Connect Frontend to API
**Action:** Integrate frontend with backend API.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Implement API service integration in the React frontend:
- Create API client with Axios
- Set up request/response interceptors
- Implement retry logic
- Add authentication headers if needed
- Create typed wrapper functions for each API endpoint
- Add proper error handling
```

#### Step 36: Implement Data Caching
**Action:** Add client-side caching.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Implement a caching strategy for API requests in the React application:
- Create a cache service using React Query or a similar library
- Set appropriate cache durations for different data types
- Implement cache invalidation rules
- Add background refresh capabilities
- Optimize for map data to reduce redundant requests
```

#### Step 37: Add Report Request Interface
**Action:** Create UI for requesting reports.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a React component for requesting crash reports for a selected road:
- Form for configuring report options
- Date range selection
- Report format options (PDF, CSV)
- Detail level selection
- Preview capability
- Status indication during generation
- Error handling
```

### Day 13: AI Integration for Reports

#### Step 38: Set Up Claude API Integration
**Action:** Integrate with Claude API for report insights.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a service that uses the Claude API to analyze crash data patterns and generate insights for reports.
The service should:
- Format crash data for optimal Claude prompt construction
- Send well-structured requests to the Claude API
- Process and structure the responses
- Handle rate limiting and errors
- Cache results when appropriate
```

#### Step 39: Implement Report Summarization
**Action:** Add AI-powered report summaries.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create a module that uses Claude API to generate executive summaries for crash reports.
Features should include:
- Identifying key patterns and trends
- Highlighting significant findings
- Comparing with similar roads or previous periods
- Generating natural language descriptions of data
- Formatting the summary for inclusion in reports
```

#### Step 40: Create Pattern Analysis Component
**Action:** Implement AI-driven pattern analysis.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a component that uses Claude API to analyze crash patterns beyond basic statistics.
The component should:
- Identify unusual patterns or anomalies
- Generate hypotheses about contributing factors
- Compare patterns across similar roads
- Visualize pattern relationships
- Present insights in user-friendly language
```

### Day 14: Testing and Integration

#### Step 41: Implement End-to-End Testing
**Action:** Create E2E tests for main user flows.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create end-to-end tests using Cypress or a similar framework for the main user flows:
- Viewing the map and toggling layers
- Filtering crash data
- Selecting a road and viewing details
- Generating and downloading a report
Include test utilities, fixtures, and proper assertions.
```

#### Step 42: API Integration Testing
**Action:** Test frontend-backend integration.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create integration tests for the API endpoints that:
- Test data retrieval with various parameters
- Verify filtering functionality
- Check error handling
- Test authentication if applicable
- Validate report generation
Include test fixtures and helper functions.
```

## Phase 3: Feature Completion and Enhancement (Days 15-25)

### Day 15-16: Map Enhancements

#### Step 43: Implement Map Clustering
**Action:** Add marker clustering for performance.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Enhance the map component with marker clustering:
- Implement clustering for crash data points
- Configure clustering radius based on zoom level
- Create custom cluster icons that show severity distribution
- Ensure clusters break apart smoothly on zoom
- Optimize for performance with large datasets
```

#### Step 44: Add Heatmap Visualization
**Action:** Create heatmap view for crash density.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a heatmap visualization component for crash density using Leaflet.heat or a similar library:
- Configure intensity based on crash severity
- Add a gradient legend
- Implement controls for adjusting heatmap parameters
- Ensure good performance with large datasets
- Make transitions smooth when toggling the heatmap
```

### Day 17-18: Advanced Filtering

#### Step 45: Create Advanced Filter Panel
**Action:** Implement comprehensive filtering interface.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create an advanced filter panel component that allows detailed filtering of crash data:
- Multiple filter categories (weather, time of day, vehicle types, etc.)
- Collapsible sections for each filter category
- Visual indication of active filters
- Quick reset options
- Filter combinations (AND/OR logic)
- Filter presets/saved filters
```

#### Step 46: Implement Custom Time Filters
**Action:** Add time-based filtering options.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Create time-based filtering components for crash data:
- Time of day ranges
- Day of week selection
- Month/season patterns
- Year-over-year comparison
- Visual indicators for temporal patterns
- Integration with existing filter system
```

### Day 19-20: Road Comparison Feature

#### Step 47: Implement Road Comparison Interface
**Action:** Create UI for comparing multiple roads.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Create components for comparing safety statistics between multiple roads:
- Interface for selecting up to 3 roads to compare
- Side-by-side comparison of key metrics
- Comparative charts for crash rates and severity
- Normalization based on road length or traffic volume
- Visual highlighting of significant differences
```

#### Step 48: Add Statistical Analysis Component
**Action:** Implement statistical comparison.
**AI Tool:** Claude Sonnet 3.5
**Prompt:**
```
Create a statistical analysis component that:
- Calculates normalized crash rates
- Performs statistical significance testing
- Generates confidence intervals
- Visualizes statistical comparisons
- Identifies statistically significant differences between roads
- Explains findings in accessible language
```

### Day 21-22: Mobile Optimization

#### Step 49: Optimize for Mobile Devices
**Action:** Enhance mobile experience.
**AI Tool:** GitHub Copilot
**Prompt:**
```
Optimize the application for mobile devices:
- Create responsive layouts for small screens
- Implement touch-friendly controls for map interaction
- Create collapsible panels to maximize map space
- Optimize performance for mobile browsers
- Add mobile-specific features (geolocation, etc.)
```

#### Step 50: Implement Progressive Enhancement
**Action:** Add progressive enhancement features.
**AI Tool:** Claude in Cursor
**Prompt:**
```
Implement progressive enhancement features:
- Offline support for basic functionality
- Data persistence for recently viewed roads
- Service worker for caching assets and data
- Reduced data mode for limited connections
- Cross-browser compatibility optimizations
```

## Implementation Timeline

### Milestone 1: MVP (End of Week 2)
- Basic map visualization with crash data
- Layer toggling functionality
- Simple filtering capabilities
- Road selection interface

### Milestone 2: Core Functionality (End of Week 4)
- Complete data integration (crash, traffic, AADT)
- Advanced filtering options
- Road details panel
- Basic report generation

### Milestone 3: Enhanced Features (End of Week 7)
- AI-powered report insights
- Road comparison tools
- Statistical analysis
- Mobile optimization
- Complete documentation

## Debugging Strategies

Throughout the implementation, use these AI-assisted debugging approaches:

1. **For Data Issues:**
   - Use Claude Sonnet 3.5 to analyze data structures and identify inconsistencies
   - Prompt: "Analyze this crash data sample and identify potential issues with format, missing values, or inconsistencies"

2. **For Map Rendering Problems:**
   - Use Gemini Flash to debug Leaflet integration issues
   - Prompt: "Debug this Leaflet map rendering issue where markers are not appearing correctly"

3. **For Performance Optimization:**
   - Use Claude in Cursor to profile and optimize React components
   - Prompt: "Analyze this React component for performance bottlenecks and suggest optimizations"

4. **For API Integration:**
   - Use GitHub Copilot to debug API request/response issues
   - Prompt: "Help debug this API integration issue where the frontend is not receiving the expected response format"

## Potential Challenges and Solutions

### Challenge: Large Dataset Performance
**Solution:** Implement progressive loading, clustering, and viewport-based filtering.
**AI Prompt:**
```
Suggest optimization strategies for rendering thousands of data points on a Leaflet map
without sacrificing performance or user experience.
```

### Challenge: Complex Geospatial Queries
**Solution:** Leverage PostGIS capabilities and optimize query patterns.
**AI Prompt:**
```
Generate efficient PostGIS queries for finding crashes along a specific road segment,
taking into account proximity to the road and filtering by various attributes.
```

### Challenge: Cross-Browser Compatibility
**Solution:** Implement thorough testing and progressive enhancement.
**AI Prompt:**
```
Review this React component for potential cross-browser compatibility issues,
particularly with the Leaflet map integration and geolocation features.
```

### Challenge: Mobile User Experience
**Solution:** Implement responsive design with mobile-specific optimizations.
**AI Prompt:**
```
Redesign this filter interface for optimal mobile usability while maintaining
all functionality. Consider touch targets, screen real estate, and device capabilities.
```

This 50-step implementation plan provides a comprehensive roadmap for building the road.engineering platform. By following this structured approach and leveraging AI tools effectively, development can be accelerated while maintaining high quality standards. The plan balances technical implementation with user experience considerations, ensuring that the final product delivers value to all stakeholders.
