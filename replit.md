# Transport Morneau Quote Generator

## Overview

This is a full-stack web application for Transport Morneau, a transportation company that generates shipping quotes. The application features a React frontend with a Node.js/Express backend, using PostgreSQL for data storage and Drizzle ORM for database operations.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

The application follows a modern full-stack architecture with clear separation between frontend, backend, and database layers:

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **Build Tool**: Vite for fast development and optimized builds
- **UI Library**: Radix UI components with Tailwind CSS styling
- **Routing**: Wouter for lightweight client-side routing
- **State Management**: TanStack Query for server state management
- **Styling**: Tailwind CSS with custom CSS variables for theming

### Backend Architecture
- **Runtime**: Node.js with Express.js framework
- **Language**: TypeScript with ES modules
- **API Style**: RESTful API endpoints
- **Database ORM**: Drizzle ORM with PostgreSQL
- **Database Provider**: Neon Database (serverless PostgreSQL)

### Key Design Decisions
1. **Monorepo Structure**: Single repository with client, server, and shared directories
2. **Type Safety**: Full TypeScript implementation across frontend and backend
3. **Shared Schema**: Common type definitions in `/shared` directory for consistency
4. **Modern Build Pipeline**: Vite for frontend, esbuild for backend production builds

## Key Components

### Database Schema (`shared/schema.ts`)
- **Users Table**: Authentication and user management
- **Quotes Table**: Comprehensive quote storage with all shipping details
- **Validation**: Zod schemas for runtime type validation

### API Endpoints (`server/routes.ts`)
- `POST /api/distance` - Calculate shipping distance between locations
- `POST /api/quotes` - Create new shipping quotes
- `GET /api/quotes/recent` - Retrieve recent quotes
- `GET /api/places/autocomplete` - Google Maps address autocomplete proxy
- `GET /api/places/details` - Google Maps place details proxy
- `GET /api/fuel-surcharge` - Real-time fuel surcharge rates from Transport Morneau

### Frontend Features
- **Multi-language Support**: French and English translations
- **Theme Support**: Light/dark mode switching
- **Responsive Design**: Mobile-first approach with Tailwind CSS
- **Form Handling**: React Hook Form with Zod validation
- **PDF Generation**: Professional PDF generation with Transport Morneau's official layout and branding
- **Address Autocomplete**: Google Maps Places API integration with Canadian address suggestions (fixed focus issues)
- **Company Search**: Searchable dropdown with 49+ Canadian agribusiness companies
- **Real Distance Calculation**: Google Maps Distance Matrix API for accurate shipping distances
- **Accurate Quote Calculation**: Updated calculation logic following Transport Morneau's exact pricing formulas
- **Automatic TL/LTL Classification**: Auto-determines shipment type based on weight (10,000+ lbs = TL, <10,000 lbs = LTL)
- **Visual Fuel Surcharge Display**: Live fuel surcharge rates prominently displayed with interactive classification cards

### Storage Layer (`server/storage.ts`)
- **Interface-based Design**: IStorage interface for flexible storage implementations
- **PostgreSQL Database**: Now using DatabaseStorage with Drizzle ORM for production persistence
- **Database Schema**: Full schema with users and quotes tables deployed via Drizzle migrations

## Data Flow

1. **Quote Creation Flow**:
   - User fills out quote form in React frontend
   - Form data validated using Zod schemas
   - Distance calculated via API endpoint
   - Quote data sent to backend via POST request
   - Backend validates and stores quote in database
   - Success response returned to frontend

2. **Data Validation**:
   - Shared Zod schemas ensure consistent validation
   - Frontend validates before submission
   - Backend re-validates for security

3. **PDF Generation**:
   - Client-side PDF generation using official Transport Morneau format
   - Includes all required fields: shipper, consignee, payment terms, dimensions, cubic feet calculation
   - Professional navy blue and white branding matching company standards
   - Legal disclaimers and contact information in both languages
   - Filename format: soumission_[companyName]_[date].pdf

4. **Quote Calculation Logic** (Updated January 2025):
   - **Automatic Classification**: ≥10,000 lbs = TL, <10,000 lbs = LTL
   - Step 1: Base Rate Total = qty × rate/pallet (LTL: $69.33, TL: varies)
   - Step 2: Excess Weight Charge = qty × $0.20/pallet (if weight > 3900 lb)
   - Step 3: Fuel Surcharge = fuel_rate × base_rate (auto-determined: LTL 28%, TL 56%)
   - Step 4: Subtotal = base_rate + excess_charge + fuel_surcharge
   - Step 5: Taxes = GST 5% + QST 9.975% on subtotal
   - Step 6: Final Total = subtotal + taxes

5. **Visual Fuel Surcharge Display**:
   - Interactive classification cards showing LTL (28%) and TL (56%) rates
   - Active classification highlighted in Transport Morneau blue
   - Real-time fuel surcharge rates from Transport Morneau website
   - Auto-updates based on pallet count input

## External Dependencies

### Production Dependencies
- **Database**: PostgreSQL database with live connection
- **ORM**: Drizzle ORM for type-safe database operations with neon-serverless adapter
- **Fuel Surcharge API**: Live data integration from Transport Morneau website
- **UI Components**: Radix UI for accessible component primitives
- **Styling**: Tailwind CSS for utility-first styling
- **Forms**: React Hook Form with Hookform Resolvers
- **Validation**: Zod for schema validation
- **State Management**: TanStack React Query
- **Date Handling**: date-fns for date manipulation

### Development Dependencies
- **Build Tools**: Vite, esbuild, PostCSS
- **Type Checking**: TypeScript with strict configuration
- **Development**: tsx for TypeScript execution

### Optional Integrations
- **Google Maps Places API**: Address autocomplete and validation (GOOGLE_MAPS_API_KEY required)
- **Mapping APIs**: Google Maps or MapBox for distance calculations (API keys required)
- **Replit Integration**: Special handling for Replit development environment

## Deployment Strategy

### Development
- **Frontend**: Vite dev server with hot module replacement
- **Backend**: tsx for TypeScript execution with nodemon-like behavior
- **Database**: Local PostgreSQL or Neon Database
- **Environment**: Development environment detection and specific tooling

### Production Build
- **Frontend**: Static build output to `dist/public`
- **Backend**: Bundled with esbuild to `dist/index.js`
- **Database**: PostgreSQL with Drizzle migrations
- **Environment Variables**: `DATABASE_URL` required for production

### Database Management
- **Migrations**: Drizzle Kit for schema migrations
- **Connection**: Environment-based database URL configuration
- **Schema**: Centralized schema definition with automatic type generation

### Scalability Considerations
- **Stateless Backend**: No session state, suitable for horizontal scaling
- **Database**: PostgreSQL supports connection pooling and read replicas
- **Frontend**: Static assets can be served via CDN
- **API**: RESTful design allows for easy caching and load balancing

### Security Features
- **Input Validation**: Comprehensive validation on both client and server
- **Type Safety**: TypeScript prevents many runtime errors
- **Environment Variables**: Sensitive data stored in environment variables
- **CORS**: Express.js CORS configuration for cross-origin requests