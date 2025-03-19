# Road.Engineering Project Documentation

This repository contains comprehensive documentation for the road.engineering project, which aims to develop a web-based platform for visualizing and analyzing Queensland road crash data and Average Annual Daily Traffic (AADT) data.

## Project Overview

The road.engineering platform will allow users to:
- View an interactive map showing crash data and AADT data for Queensland roads
- Toggle between different data layers (crash, traffic, AADT)
- Select specific roads for detailed analysis
- Generate comprehensive crash data reports for selected roads
- Analyze historical crash patterns and identify potential safety issues

The platform utilizes geospatial data from the Queensland Government, including:
- [Queensland Road Crash Database](https://www.data.qld.gov.au/dataset/crash-data-from-queensland-roads)
- [Queensland Traffic Census for the State-Declared Road Network](https://www.data.qld.gov.au/dataset/traffic-census-for-the-queensland-state-declared-road-network)

## Documentation Contents

This repository includes:

1. **[Project Requirement Document (PRD)](./1_Project_Requirement_Document.md)** - Outlines the project's purpose, target audience, core features, success metrics, and constraints.

2. **[Tech Stack Document](./2_Tech_Stack_Document.md)** - Details the recommended technology stack with justifications for why these choices are optimal for AI-assisted development, geospatial mapping, and data visualization.

3. **[Frontend Guidelines](./3_Frontend_Guidelines.md)** - Provides best practices for frontend development, including UI/UX design principles, component architecture, responsive design requirements, and coding standards.

4. **[Backend Structure Document](./4_Backend_Structure_Document.md)** (Parts [1](./4_Backend_Structure_Document.md) and [2](./4_Backend_Structure_Document_part2.md)) - Describes the backend architecture, API endpoints, data flow, and server-side logic.

5. **[App Flow Document](./5_App_Flow_Document.md)** - Provides a step-by-step flow diagram of how users will interact with the website, highlighting key interactions where AI could assist.

6. **[50-Step Implementation Plan](./6_50_Step_Implementation_Plan.md)** - Details a comprehensive, step-by-step plan for building the project using AI coding tools, with specific prompts for AI models.

7. **[Starter Kit/Boilerplate Suggestions](./7_Starter_Kit_Boilerplate_Suggestions.md)** - Recommends pre-built starter kits or boilerplates to accelerate development.

## Getting Started

For developers looking to contribute to or build the road.engineering platform:

1. Review the documentation in the order listed above to understand the project requirements and architecture
2. Follow the recommended tech stack and implementation approach outlined in the Tech Stack Document
3. Use the 50-Step Implementation Plan as a roadmap for development
4. Consider utilizing one of the recommended starter kits from the Starter Kit/Boilerplate Suggestions document
5. Follow the frontend and backend guidelines for consistent development

## Key Technologies

The platform will be built using:

### Frontend
- React with TypeScript
- Leaflet.js for map visualization
- Tailwind CSS for styling
- React Query for data fetching
- Recharts for data visualization

### Backend
- Node.js with Express or FastAPI
- PostgreSQL with PostGIS for geospatial data
- Redis for caching
- Claude API for AI-powered report generation
- JWT for authentication

## Development Approach

The project follows an AI-assisted development approach, utilizing:

- Claude Sonnet 3.5/3.7 for code generation and insights
- GitHub Copilot for in-IDE assistance
- Cursor for complex component development
- AI-driven pattern recognition for crash data analysis

## License

This documentation is provided for planning and development purposes only. The actual implementation of road.engineering will be subject to appropriate licensing and data usage policies.

## Contact

For questions or clarifications regarding this documentation, please contact the project maintainers.
