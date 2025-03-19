# Frontend Guidelines - road.engineering

This document outlines the frontend development standards, best practices, UI/UX principles, and coding standards for the road.engineering project. These guidelines ensure consistency, maintainability, and a high-quality user experience across the platform.

## 1. UI/UX Design Principles

### Core Principles

1. **Clarity First**: 
   - Prioritize clear presentation of crash and traffic data
   - Use intuitive visual hierarchy for map elements
   - Avoid unnecessary visual elements that distract from the data

2. **Progressive Disclosure**:
   - Present essential information first, with options to view more details
   - Use expanding panels, tooltips, and drill-downs for detailed crash information
   - Layer complexity gradually as users engage with the interface

3. **Consistency**:
   - Maintain consistent color schemes, terminology, and interaction patterns
   - Use standard map controls and conventions familiar to users
   - Apply uniform styling to UI components throughout the application

4. **Accessibility**:
   - Design for users with varying abilities and needs
   - Support keyboard navigation for all interactive elements
   - Ensure sufficient color contrast for text and important map features
   - Provide alternative ways to access information shown on maps

5. **Performance**:
   - Optimize for map rendering with large datasets
   - Implement progressive loading techniques
   - Provide visual feedback during data loading and processing

### Color System

- **Primary Palette**:
  - Primary Blue: `#0066cc` - Main brand color
  - Secondary Blue: `#003366` - Headers and important elements
  - Accent Yellow: `#ffcc00` - Call to action and highlights

- **Data Visualization Colors**:
  - Fatal crashes: `#ff0000` (Red)
  - Serious crashes: `#ff6600` (Orange)
  - Minor crashes: `#ffcc00` (Yellow)
  - Traffic congestion: `#ff0000` (Red)
  - Road works: `#ffa500` (Orange)
  - AADT data: `rgba(0, 102, 255, 0.4)` (Transparent Blue)

- **UI Colors**:
  - Background: `#ffffff` (White)
  - Secondary Background: `#f5f5f5` (Light Gray)
  - Text: `#000000` (Black)
  - Secondary Text: `#666666` (Dark Gray)
  - Borders: `#cccccc` (Medium Gray)

### Typography

- **Font Family**:
  - Primary: System font stack (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`)
  - Consider supplementing with a web font like Inter or Roboto for better cross-platform consistency

- **Font Sizes**:
  - Heading 1: 24px, Bold
  - Heading 2: 20px, Bold
  - Heading 3: 18px, Semi-bold
  - Body Text: 16px, Regular
  - Small Text: 14px, Regular
  - Map Labels: 12px, Medium

- **Line Heights**:
  - Headings: 1.2
  - Body Text: 1.5
  - Small Text: 1.4

## 2. Component Architecture

### Key UI Components

#### 1. Map Container

The central map component should:
- Occupy the primary viewport space
- Support touch and mouse interactions
- Include standard map controls (zoom, pan, etc.)
- Handle responsive resizing
- Support layer toggling

```tsx
// Map Container Component Example
import React, { useRef, useEffect, useState } from 'react';
import { LayerType } from './types';

interface MapContainerProps {
  activeLayer: LayerType;
  onSelectRoad?: (roadId: string) => void;
}

const MapContainer: React.FC<MapContainerProps> = ({ 
  activeLayer, 
  onSelectRoad 
}) => {
  const mapRef = useRef<HTMLDivElement>(null);
  
  // Implementation details...
  
  return (
    <div 
      className="w-full h-full relative"
      role="application"
      aria-label="Queensland road safety map"
    >
      <div ref={mapRef} className="absolute inset-0" />
      
      {/* Map Controls */}
      <div className="absolute top-4 right-4 z-10 bg-white p-2 rounded shadow">
        {/* Zoom controls, layer toggles, etc. */}
      </div>
      
      {/* Loading indicator */}
      {isLoading && (
        <div className="absolute inset-0 flex items-center justify-center bg-white/70 z-20">
          <div className="text-xl font-semibold">Loading map data...</div>
        </div>
      )}
    </div>
  );
};
```

#### 2. Layer Control Panel

A control panel for toggling map layers:
- Clear visual indication of active layers
- Compact, accessible design
- Optional layer legends
- Support for layer opacity adjustment

```tsx
// Layer Control Component Example
import React from 'react';
import { LayerType } from './types';

interface LayerControlProps {
  activeLayer: LayerType;
  setActiveLayer: (layer: LayerType) => void;
}

const LayerControl: React.FC<LayerControlProps> = ({
  activeLayer,
  setActiveLayer
}) => {
  return (
    <div className="flex gap-4 p-4 bg-black text-white">
      <button 
        className={`px-4 py-2 rounded border border-white
          ${activeLayer === 'crash' 
            ? 'bg-white text-black' 
            : 'bg-black text-white'}`}
        onClick={() => setActiveLayer('crash')}
        aria-pressed={activeLayer === 'crash'}
      >
        Crash Data
      </button>
      
      <button 
        className={`px-4 py-2 rounded border border-white
          ${activeLayer === 'traffic' 
            ? 'bg-white text-black' 
            : 'bg-black text-white'}`}
        onClick={() => setActiveLayer('traffic')}
        aria-pressed={activeLayer === 'traffic'}
      >
        Live Traffic
      </button>
      
      <button 
        className={`px-4 py-2 rounded border border-white
          ${activeLayer === 'aadt' 
            ? 'bg-white text-black' 
            : 'bg-black text-white'}`}
        onClick={() => setActiveLayer('aadt')}
        aria-pressed={activeLayer === 'aadt'}
      >
        AADT Data
      </button>
    </div>
  );
};
```

#### 3. Road Selection Interface

Components for selecting roads:
- Search input with autocomplete for road names
- Map-based selection tool
- Recent selections history
- Saved/favorite roads section

```tsx
// Road Selection Component Example
import React, { useState } from 'react';

interface RoadSelectorProps {
  onSelectRoad: (roadId: string) => void;
}

const RoadSelector: React.FC<RoadSelectorProps> = ({ onSelectRoad }) => {
  const [searchQuery, setSearchQuery] = useState('');
  const [searchResults, setSearchResults] = useState<Array<{id: string, name: string}>>([]);
  
  // Search implementation details...
  
  return (
    <div className="p-4 bg-white rounded shadow">
      <h2 className="text-xl font-bold mb-4">Select a Road</h2>
      
      <div className="relative">
        <input
          type="text"
          placeholder="Search for a road..."
          className="w-full p-2 border rounded"
          value={searchQuery}
          onChange={(e) => setSearchQuery(e.target.value)}
          aria-label="Road search"
        />
        
        {searchResults.length > 0 && (
          <ul className="absolute z-10 w-full bg-white border rounded mt-1 shadow-lg max-h-60 overflow-y-auto">
            {searchResults.map((road) => (
              <li key={road.id} className="p-2 hover:bg-gray-100 cursor-pointer">
                <button 
                  className="w-full text-left"
                  onClick={() => onSelectRoad(road.id)}
                >
                  {road.name}
                </button>
              </li>
            ))}
          </ul>
        )}
      </div>
      
      <div className="mt-4">
        <h3 className="font-semibold mb-2">Recent Selections</h3>
        {/* Recent roads list */}
      </div>
    </div>
  );
};
```

#### 4. Report Generation Panel

Interface for customizing and generating reports:
- Filterable options for report content
- Date range selection
- Export format options
- Report preview

```tsx
// Report Generation Component Example
import React, { useState } from 'react';

interface ReportPanelProps {
  roadId?: string;
  roadName?: string;
  onGenerateReport: (options: ReportOptions) => void;
}

interface ReportOptions {
  roadId: string;
  dateRange: [Date, Date];
  includeCharts: boolean;
  includeCrashDetails: boolean;
  format: 'pdf' | 'csv';
}

const ReportPanel: React.FC<ReportPanelProps> = ({ 
  roadId, 
  roadName,
  onGenerateReport 
}) => {
  const [dateRange, setDateRange] = useState<[Date, Date]>([
    new Date(2010, 0, 1),
    new Date()
  ]);
  
  const [includeCharts, setIncludeCharts] = useState(true);
  const [includeCrashDetails, setIncludeCrashDetails] = useState(true);
  const [format, setFormat] = useState<'pdf' | 'csv'>('pdf');
  
  // Implementation details...
  
  return (
    <div className="p-4 bg-white rounded shadow">
      <h2 className="text-xl font-bold mb-4">Generate Report</h2>
      
      {roadId ? (
        <div>
          <p className="mb-4">Generating report for: <strong>{roadName}</strong></p>
          
          {/* Report options form */}
          <div className="space-y-4">
            {/* Date range selection */}
            <div>
              <label className="block mb-2 font-medium">Date Range</label>
              {/* Date picker implementation */}
            </div>
            
            {/* Report content options */}
            <div>
              <label className="flex items-center">
                <input
                  type="checkbox"
                  checked={includeCharts}
                  onChange={(e) => setIncludeCharts(e.target.checked)}
                  className="mr-2"
                />
                Include statistical charts
              </label>
              
              <label className="flex items-center mt-2">
                <input
                  type="checkbox"
                  checked={includeCrashDetails}
                  onChange={(e) => setIncludeCrashDetails(e.target.checked)}
                  className="mr-2"
                />
                Include detailed crash listings
              </label>
            </div>
            
            {/* Format selection */}
            <div>
              <label className="block mb-2 font-medium">Format</label>
              <div className="flex gap-4">
                <label className="flex items-center">
                  <input
                    type="radio"
                    name="format"
                    checked={format === 'pdf'}
                    onChange={() => setFormat('pdf')}
                    className="mr-2"
                  />
                  PDF
                </label>
                
                <label className="flex items-center">
                  <input
                    type="radio"
                    name="format"
                    checked={format === 'csv'}
                    onChange={() => setFormat('csv')}
                    className="mr-2"
                  />
                  CSV (Raw Data)
                </label>
              </div>
            </div>
            
            <button
              className="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
              onClick={() => onGenerateReport({
                roadId,
                dateRange,
                includeCharts,
                includeCrashDetails,
                format
              })}
            >
              Generate Report
            </button>
          </div>
        </div>
      ) : (
        <p>Please select a road on the map first</p>
      )}
    </div>
  );
};
```

#### 5. Map Legend Component

Clear visual indicators of what the map symbols represent:
- Color-coded key for crash severity
- AADT traffic volume indicators
- Traffic incident type symbols

```tsx
// Map Legend Component Example
import React from 'react';
import { LayerType } from './types';

interface MapLegendProps {
  activeLayer: LayerType;
}

const MapLegend: React.FC<MapLegendProps> = ({ activeLayer }) => {
  return (
    <div className="absolute bottom-4 left-4 z-10 bg-white p-3 rounded shadow max-w-xs">
      <h3 className="font-bold text-sm mb-2">Legend</h3>
      
      {activeLayer === 'crash' && (
        <div className="space-y-2">
          <div className="flex items-center">
            <div className="w-4 h-4 rounded-full bg-red-600 border border-black mr-2"></div>
            <span className="text-sm">Fatal Crash</span>
          </div>
          <div className="flex items-center">
            <div className="w-4 h-4 rounded-full bg-orange-500 border border-black mr-2"></div>
            <span className="text-sm">Serious Injury Crash</span>
          </div>
          <div className="flex items-center">
            <div className="w-4 h-4 rounded-full bg-yellow-400 border border-black mr-2"></div>
            <span className="text-sm">Minor Injury Crash</span>
          </div>
        </div>
      )}
      
      {activeLayer === 'traffic' && (
        <div className="space-y-2">
          {/* Traffic incident legend items */}
        </div>
      )}
      
      {activeLayer === 'aadt' && (
        <div className="space-y-2">
          {/* AADT data legend items */}
        </div>
      )}
    </div>
  );
};
```

## 3. Responsive Design Guidelines

The road.engineering platform must be fully functional across a range of devices and screen sizes:

### Breakpoints

- **Mobile**: 320px - 639px
- **Tablet**: 640px - 1023px
- **Desktop**: 1024px+

### Layout Adaptations

1. **Mobile Layout**:
   - Full-width map with collapsible panels
   - Simplified controls with dropdown menus
   - Stack UI panels vertically
   - Focus on essential data with options to view more

2. **Tablet Layout**:
   - Split screen with map taking approximately 70% of width
   - Side panel for controls and data
   - Collapsible sections for filters and options
   - Touch-friendly controls with appropriate spacing

3. **Desktop Layout**:
   - Maximized map view with floating panels
   - Multi-column layouts for detailed data views
   - Advanced filtering options visible
   - Support for side-by-side comparisons

### Implementation

Use Tailwind CSS responsive prefixes (sm:, md:, lg:, xl:) to control layout at different breakpoints:

```tsx
<div className="flex flex-col md:flex-row w-full h-screen">
  {/* Map container - full height on mobile, full width on desktop */}
  <div className="h-1/2 md:h-full md:w-2/3 lg:w-3/4 relative">
    <MapContainer />
  </div>
  
  {/* Controls panel - full width on mobile, sidebar on desktop */}
  <div className="h-1/2 md:h-full md:w-1/3 lg:w-1/4 bg-white overflow-y-auto">
    <LayerControl />
    <RoadSelector />
    <ReportPanel />
  </div>
</div>
```

## 4. Coding Standards

### TypeScript Guidelines

- Use TypeScript for all new components and modules
- Define interfaces for all props and state
- Use enums for fixed sets of values (e.g., crash severity levels)
- Leverage union types for related values (e.g., `type LayerType = 'crash' | 'traffic' | 'aadt'`)
- Avoid `any` type; use `unknown` when type is truly undetermined
- Use type guards to narrow types when necessary

```tsx
// Type definitions example
// src/types/index.ts

export type LayerType = 'crash' | 'traffic' | 'aadt';

export interface Coordinates {
  latitude: number;
  longitude: number;
}

export interface CrashData {
  id: string;
  coordinates: Coordinates;
  severity: 'Fatal' | 'Serious' | 'Minor';
  date: string;
  roadName?: string;
  casualties?: number;
  vehicles?: number;
  // Additional properties...
}

export interface TrafficData {
  id: string;
  coordinates: Coordinates;
  type: 'Congestion' | 'Roadwork' | 'Accident' | 'Closure';
  status: 'Active' | 'Cleared' | 'Scheduled';
  updatedAt: string;
  // Additional properties...
}

export interface AADTData {
  id: string;
  coordinates: Coordinates;
  roadName: string;
  count: number;
  year: number;
  // Additional properties...
}
```

### React Best Practices

1. **Component Structure**:
   - Use functional components with hooks
   - Keep components focused on a single responsibility
   - Extract complex logic into custom hooks
   - Implement React.memo for performance optimization when appropriate

2. **State Management**:
   - Use React Context for global state (e.g., selected road, active layer)
   - Leverage React Query for data fetching and caching
   - Implement local component state for UI-specific concerns
   - Consider using reducers for complex state logic

3. **Performance Optimizations**:
   - Use virtualized lists for long data sets (crash listings, road selections)
   - Implement lazy loading for components not needed on initial render
   - Use React.lazy and Suspense for code splitting
   - Memoize expensive calculations and heavy components

### Code Style

- Follow a consistent naming convention:
  - PascalCase for components and interfaces
  - camelCase for variables, functions, and props
  - UPPER_SNAKE_CASE for constants
  
- File Structure:
  - Group related components in feature folders
  - Use index files to simplify imports
  - Keep files focused on a single component or hook

```
src/
├── components/
│   ├── map/
│   │   ├── index.tsx                 # Re-exports
│   │   ├── MapContainer.tsx          # Main map component
│   │   ├── LayerControl.tsx          # Layer toggling
│   │   ├── MapLegend.tsx             # Map legend
│   │   └── hooks/
│   │       ├── useMapInitialization.ts
│   │       └── useMarkerManagement.ts
│   ├── reports/
│   │   ├── index.tsx
│   │   ├── ReportPanel.tsx
│   │   └── ReportPreview.tsx
│   └── common/
│       ├── Button.tsx
│       └── Modal.tsx
├── hooks/
│   ├── useDataFetching.ts
│   └── useGeolocation.ts
├── types/
│   └── index.ts
└── utils/
    ├── api.ts
    └── mapHelpers.ts
```

## 5. Accessibility Guidelines

### WCAG Compliance

The road.engineering platform must adhere to WCAG 2.1 Level AA standards:

1. **Keyboard Navigation**:
   - All interactive elements must be keyboard accessible
   - Implement focus management for modals and panels
   - Provide visible focus indicators
   - Support standard keyboard shortcuts for map navigation

2. **Screen Reader Support**:
   - Include appropriate ARIA attributes for custom components
   - Provide text alternatives for map features
   - Implement descriptive labels for form controls
   - Ensure dynamic content changes are announced appropriately

3. **Color and Contrast**:
   - Maintain minimum contrast ratio of 4.5:1 for normal text
   - Do not rely solely on color to convey meaning
   - Provide alternative indicators (patterns, labels, etc.)
   - Test with color blindness simulators

### Implementation Examples

```tsx
// Accessible map marker popup
<div 
  role="dialog"
  aria-labelledby="crash-popup-title"
  className="map-popup"
>
  <h3 id="crash-popup-title" className="text-lg font-bold">
    Crash Details
  </h3>
  <div className="mt-2">
    <p>Severity: <span className="font-medium">{crash.severity}</span></p>
    <p>Date: <span className="font-medium">{formatDate(crash.date)}</span></p>
    <p>Time: <span className="font-medium">{formatTime(crash.time)}</span></p>
    {/* Additional details */}
  </div>
  <button
    className="absolute top-2 right-2 p-1"
    aria-label="Close popup"
    onClick={closePopup}
  >
    ✕
  </button>
</div>
```

```tsx
// Screen reader text for map elements
<div className="sr-only">
  This map shows {activeLayer === 'crash' ? 'crash data' : 
                 activeLayer === 'traffic' ? 'traffic incidents' : 
                 'annual average daily traffic data'} 
  for Queensland roads. There are currently {visibleMarkers} 
  data points visible in the current view.
</div>
```

## 6. Performance Optimization

### Loading Strategies

1. **Progressive Data Loading**:
   - Load data within the current map bounds first
   - Implement clustering for large datasets
   - Use pagination for API requests
   - Cache previously loaded areas

2. **Asset Optimization**:
   - Optimize and lazy-load images
   - Use vector icons where possible
   - Implement code splitting for route-based components
   - Minify and compress all static assets

3. **Rendering Optimization**:
   - Use canvas rendering for dense data visualizations
   - Implement virtualized rendering for large lists
   - Throttle map event handlers (zoom, pan)
   - Debounce search inputs and filters

### Example Implementation

```tsx
// Performance-optimized marker loading
import { useEffect, useRef } from 'react';
import { useQuery } from 'react-query';
import { fetchCrashData } from 'api/crashData';

function useOptimizedMarkers(map, bounds) {
  const markersRef = useRef([]);
  
  // React Query with bounds as key for caching
  const { data, isLoading } = useQuery(
    ['crashData', bounds.toString()],
    () => fetchCrashData(bounds),
    {
      staleTime: 5 * 60 * 1000, // 5 minutes
      keepPreviousData: true,
    }
  );
  
  // Update markers when data or bounds change
  useEffect(() => {
    if (!data || !map) return;
    
    // Remove existing markers
    markersRef.current.forEach(marker => {
      if (marker._map) {
        marker.remove();
      }
    });
    
    // Create marker cluster group
    const markers = L.markerClusterGroup({
      disableClusteringAtZoom: 15,
      spiderfyOnMaxZoom: true,
      chunkedLoading: true,
    });
    
    // Add new markers
    data.forEach(point => {
      // Create marker with appropriate icon
      const marker = L.marker([point.latitude, point.longitude], {
        icon: getIconForCrash(point.severity)
      });
      
      // Add popup
      marker.bindPopup(createPopupContent(point));
      
      // Add to cluster group
      markers.addLayer(marker);
    });
    
    // Add cluster group to map
    map.addLayer(markers);
    
    // Store reference for cleanup
    markersRef.current = [...markers.getLayers()];
    
    return () => {
      map.removeLayer(markers);
    };
  }, [data, map, bounds]);
  
  return { isLoading };
}
```

## 7. Future Enhancements

### Potential UI Improvements

1. **3D Visualization**:
   - Implement 3D terrain models for topographical context
   - Create elevated crash markers based on severity
   - Develop 3D traffic flow visualizations

2. **Advanced Filtering**:
   - Multi-parameter filtering interface
   - Saved filter configurations
   - Visual filter builder

3. **Interactive Dashboard**:
   - Real-time statistics panels
   - Trend analysis charts
   - Comparative road safety metrics

4. **Mobile-Optimized View**:
   - Location-aware interface showing nearby incidents
   - Turn-by-turn safety ratings
   - Offline capability for field use

### Experimental Features

Consider implementing feature flags for experimental capabilities:

```tsx
// Feature flag implementation
import { useFeatureFlag } from 'hooks/useFeatureFlags';

function EnhancedReportPanel() {
  const { isEnabled: is3DEnabled } = useFeatureFlag('3d-visualization');
  const { isEnabled: isAIEnabled } = useFeatureFlag('ai-insights');
  
  return (
    <div className="report-panel">
      {/* Standard features */}
      <BasicReportOptions />
      
      {/* Experimental features */}
      {is3DEnabled && <ThreeDVisualizationOptions />}
      {isAIEnabled && <AIInsightsGenerator />}
    </div>
  );
}
```

## 8. Development Process Guidelines

### Component Development Workflow

1. **Plan and Define Requirements**:
   - Document component purpose and requirements
   - Define props interface and expected behavior
   - Create mockups or wireframes

2. **Develop Initial Version**:
   - Start with basic functionality
   - Build mobile-first, responsive layout
   - Implement accessibility features

3. **Validate and Test**:
   - Write unit tests with Jest and React Testing Library
   - Test across browsers and devices
   - Validate accessibility with automated tools

4. **Review and Refine**:
   - Conduct code review
   - Optimize for performance
   - Address edge cases

5. **Document Component**:
   - Add JSDoc comments
   - Create usage examples
   - Update component library documentation

### Example of AI-Assisted Component Development

For AI-assisted development, provide detailed instructions to your AI coding assistant:

```
Generate a React component for a crash data filter panel with the following requirements:

1. Allow filtering by crash severity (Fatal, Serious, Minor)
2. Include date range selection (year and month)
3. Filter by crash type (e.g., head-on, rear-end)
4. Support filtering by vehicle types involved
5. Use Tailwind CSS for styling
6. Include TypeScript interfaces
7. Make it responsive with mobile-first approach
8. Ensure keyboard accessibility
9. Implement as a functional component with hooks
```

This frontend guidelines document provides a comprehensive framework for developing the road.engineering interface with consistency, accessibility, and performance in mind. By following these standards, the development team can create a high-quality, user-friendly platform for visualizing Queensland's road safety data.
