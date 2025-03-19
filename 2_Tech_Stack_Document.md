# Tech Stack Document - road.engineering

This document outlines the recommended technology stack for the road.engineering project, focusing on technologies that facilitate AI-assisted development, efficient geospatial data handling, and interactive data visualization.

## 1. Frontend Technologies

### Core Framework: React with TypeScript

**Recommendation:** React 18+ with TypeScript 4.9+

**Justification:**
- Strong type safety with TypeScript reduces runtime errors and improves AI code generation quality
- React's component-based architecture fits well with the modular nature of map-based applications
- Extensive ecosystem of libraries for UI components, state management, and map integration
- Excellent AI tool support for code generation and debugging
- TypeScript's type definitions provide better code completion and documentation for developers

### UI Framework: Tailwind CSS

**Recommendation:** Tailwind CSS 3.3+

**Justification:**
- Utility-first approach works well with AI code generation
- Highly customizable design system that can be adapted to match road safety themes
- Responsive design capabilities for cross-device compatibility
- Smaller bundle size compared to component libraries
- Easy to understand class names for AI models to generate and modify

### Map Visualization: Leaflet.js

**Recommendation:** Leaflet 1.9+ with React-Leaflet wrappers

**Justification:**
- Lightweight and efficient library specialized for web-based maps
- Excellent performance with large datasets through clustering and on-demand loading
- Extensive plugin ecosystem for specialized map features
- Well-documented API that AI tools can effectively reference
- Compatible with various basemap providers (OpenStreetMap, MapBox, etc.)
- Already implemented in the current project codebase

### Data Visualization: Recharts

**Recommendation:** Recharts 2.5+

**Justification:**
- React-specific charting library with declarative components
- Built on D3.js for powerful visualization capabilities
- TypeScript support for better AI-assisted development
- Responsive design and theming capabilities
- Good accessibility features

### Build System: Vite

**Recommendation:** Vite 4.4+ (upgrade from current CRA setup)

**Justification:**
- Faster development server and build times compared to Create React App
- Better handling of large projects with many components
- Excellent TypeScript integration
- Modern module system and optimized production builds
- Easy configuration for environment variables

## 2. Backend Technologies

### API Framework: Node.js with Express

**Recommendation:** Node.js 18+ LTS with Express 4.18+

**Justification:**
- JavaScript/TypeScript consistency between frontend and backend
- Excellent performance for API endpoints and data processing
- Rich ecosystem of libraries for data transformation and geospatial operations
- Good support from AI coding tools for generating Express routes and middleware
- Scalable architecture for handling concurrent requests

### Data Processing: Python with FastAPI (Optional Microservice)

**Recommendation:** Python 3.11+ with FastAPI 0.100+

**Justification:**
- Superior data science libraries (Pandas, GeoPandas, NumPy) for complex crash data analysis
- FastAPI provides high performance and automatic API documentation
- Well suited for machine learning components (crash pattern analysis, hotspot identification)
- Excellent AI tool support for Python code generation
- Can be deployed as a separate microservice for specific data processing tasks

## 3. Database Technologies

### Primary Database: PostgreSQL with PostGIS

**Recommendation:** PostgreSQL 15+ with PostGIS 3.3+

**Justification:**
- Native geospatial capabilities through PostGIS extension
- Excellent performance for geographic queries and spatial indexing
- Advanced querying capabilities for complex crash data analysis
- ACID compliance for data integrity
- Robust support for handling large datasets with efficient indexing
- Good integration with both Node.js and Python environments

### Caching Layer: Redis

**Recommendation:** Redis 7.0+

**Justification:**
- High-performance caching for frequently accessed data
- Reduces database load for common queries
- Supports geospatial indexing for map-based queries
- Can store pre-computed report data for faster retrieval
- Reduces API response times for better user experience

## 4. Geospatial Technologies

### Primary Map API: Leaflet.js with OpenStreetMap/MapBox

**Recommendation:** Leaflet 1.9+ with OpenStreetMap or MapBox base tiles

**Justification:**
- Already implemented in the current project
- Free or cost-effective base map options
- Extensive customization options for road display
- Good performance with large datasets through clustering

### Geospatial Processing: Turf.js

**Recommendation:** Turf.js 6.5+

**Justification:**
- Advanced geospatial analysis in JavaScript
- Compatible with GeoJSON format used by Leaflet
- Supports operations like buffering, intersection, and point-in-polygon testing
- Can be used for client-side spatial operations to reduce server load
- Well-documented API that works well with AI code assistance

## 5. Data Integration

### ETL Pipeline: Node.js Scripts with node-postgres

**Recommendation:** Custom ETL scripts using Node.js with pg 8.10+

**Justification:**
- Scheduled data import from Queensland Government datasets
- Data transformation and normalization for database storage
- Validation and error handling for data quality assurance
- Ability to handle various data formats (CSV, JSON, GeoJSON)
- Can be automated through CI/CD pipelines

### API Integration: Axios

**Recommendation:** Axios 1.4+

**Justification:**
- Consistent API for HTTP requests in both browser and Node.js
- Request/response interceptors for error handling and authentication
- Support for request cancellation and timeouts
- TypeScript definitions for better code assistance

## 6. AI Integration

### Report Generation: Claude API (Anthropic)

**Recommendation:** Claude 3.5/3.7 Sonnet via Anthropic API

**Justification:**
- Excellent natural language capabilities for generating human-readable crash reports
- Support for extracting insights from structured crash data
- Can incorporate map imagery and data visualizations in reports
- Rate limiting and cost management features
- Low latency for interactive report generation

### Development Assistance: Various AI Tools

**Recommendation:** Integration of multiple AI coding assistants:
- **GitHub Copilot** for in-IDE code generation
- **Claude in Cursor** for complex component generation
- **Windsurf** for debugging and optimization

**Justification:**
- Multi-tool approach leverages strengths of different AI systems
- Assists with complex geospatial code generation
- Helps optimize performance for map rendering
- Accelerates development of data visualization components
- Supports documentation generation and maintenance

## 7. Deployment & Infrastructure

### Hosting: Vercel or AWS Amplify

**Recommendation:** Vercel for frontend, AWS services for backend

**Justification:**
- Optimized for React applications with global CDN
- Simple deployment workflow with GitHub integration
- Serverless functions for API endpoints
- Environment variable management
- Preview deployments for testing

### CI/CD: GitHub Actions

**Recommendation:** GitHub Actions with automated testing and deployment

**Justification:**
- Integrated with the GitHub repository
- Automated testing before deployment
- Scheduled workflows for data synchronization
- Environment-specific deployment pipelines
- Secrets management for API keys

## 8. Monitoring & Analytics

### Application Monitoring: Sentry

**Recommendation:** Sentry with React integration

**Justification:**
- Real-time error tracking and reporting
- Performance monitoring for map rendering
- Session replay capabilities for debugging UX issues
- Integration with CI/CD for regression detection
- User impact assessment for prioritizing fixes

### Analytics: Plausible or Google Analytics 4

**Recommendation:** Plausible for privacy-focused analytics

**Justification:**
- Privacy-compliant analytics without cookies
- Simple event tracking for user interactions
- Dashboard for monitoring key metrics
- Lightweight script with minimal performance impact
- Data ownership and control

## Integration Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Frontend                             │
│                                                             │
│  ┌─────────┐    ┌──────────┐    ┌───────────────────────┐  │
│  │ React +  │───►│ Leaflet  │───►│ Map Components        │  │
│  │ TypeScript│    │ (Maps)   │    │ - Crash Layer        │  │
│  └─────────┘    └──────────┘    │ - AADT Layer         │  │
│       │              │          │ - Traffic Layer      │  │
│       │              │          └───────────────────────┘  │
│       │              │                      │              │
│       ▼              ▼                      ▼              │
│  ┌─────────┐    ┌──────────┐    ┌───────────────────────┐  │
│  │ Tailwind │    │ Recharts │    │ Report Components     │  │
│  │ CSS      │    │ (Charts) │    │ - Export to PDF      │  │
│  └─────────┘    └──────────┘    │ - CSV Download       │  │
│                                  └───────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                │                      │
                ▼                      ▼
┌─────────────────────────────────────────────────────────────┐
│                        Backend                              │
│                                                             │
│  ┌─────────┐    ┌──────────┐    ┌───────────────────────┐  │
│  │ Node.js  │───►│ Express   │───►│ API Endpoints        │  │
│  │ API      │    │ Framework │    │ - Crash Data         │  │
│  └─────────┘    └──────────┘    │ - AADT Data          │  │
│       │                          │ - Report Generation  │  │
│       │                          └───────────────────────┘  │
│       │                                     │               │
│       ▼                                     ▼               │
│  ┌─────────┐                    ┌───────────────────────┐  │
│  │ Python   │                    │ Claude API            │  │
│  │ Data     │                    │ - Report Summaries    │  │
│  │ Processing│                    │ - Pattern Analysis    │  │
│  └─────────┘                    └───────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                │                      │
                ▼                      ▼
┌─────────────────────────────────────────────────────────────┐
│                       Data Storage                          │
│                                                             │
│  ┌─────────────────┐         ┌───────────────────────────┐  │
│  │ PostgreSQL +    │         │ Redis Cache               │  │
│  │ PostGIS         │         │ - Frequent Queries        │  │
│  │ - Crash Records │         │ - Map Tile Cache          │  │
│  │ - AADT Data     │         │ - Session Data            │  │
│  │ - Road Network  │         └───────────────────────────┘  │
│  └─────────────────┘                                        │
└─────────────────────────────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────────────────────────┐
│                     External Data                           │
│                                                             │
│  ┌─────────────────────────┐  ┌─────────────────────────┐  │
│  │ Queensland Road Crash   │  │ Queensland Traffic      │  │
│  │ Database                │  │ Census (AADT)           │  │
│  └─────────────────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Conclusion

The recommended tech stack balances modern web development practices with specialized geospatial capabilities. It leverages AI-friendly technologies that provide strong typing, clear documentation, and modular architecture, making it ideal for AI-assisted development. 

The stack is designed to be scalable and maintainable, with particular attention to the performance requirements of map-based applications handling large geospatial datasets. By using established libraries like Leaflet.js and PostGIS, the project can build on proven solutions for geospatial visualization and analysis while maintaining the flexibility to incorporate AI-generated insights and reports.
