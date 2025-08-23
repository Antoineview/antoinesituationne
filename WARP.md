# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Next.js + Sanity CMS project using a monorepo structure with workspaces. The project enables real-time visual editing, live content updates, and provides a complete headless CMS solution with a modern React frontend.

## Architecture

### Monorepo Structure
- `frontend/` - Next.js 15 application with App Router
- `studio/` - Sanity Studio for content management
- Root workspace manages both applications with npm workspaces

### Key Technologies
- **Frontend**: Next.js 15, React 19, TailwindCSS 4, TypeScript
- **CMS**: Sanity with Visual Editing, Live Content API, AI Assist
- **Styling**: TailwindCSS with Tailwind Typography
- **Development**: Turbopack, ESLint, Prettier

### Data Flow
- Content is managed in Sanity Studio (port 3333)
- Frontend fetches data using GROQ queries via Sanity client
- Real-time updates via Sanity Live Content API
- Visual editing through Sanity Presentation Tool
- Type-safe queries with generated TypeScript types

## Development Commands

### Start Development Servers
```bash
# Start both frontend and studio simultaneously
npm run dev

# Start only Next.js frontend (port 3000)
npm run dev:next

# Start only Sanity Studio (port 3333)  
npm run dev:studio
```

### Building and Type Checking
```bash
# Build both applications
npm run build --workspaces

# Type check all workspaces
npm run type-check

# Generate Sanity TypeScript types
npm run typegen --workspace=frontend
npm run extract-types --workspace=studio
```

### Code Quality
```bash
# Lint frontend code
npm run lint

# Format all code with Prettier
npm run format
```

### Testing
```bash
# Lint Next.js application
npm run lint --workspace=frontend

# Type check specific workspace
npm run type-check --workspace=frontend
npm run type-check --workspace=studio
```

### Content Management
```bash
# Import sample data into Sanity
npm run import-sample-data

# Deploy Sanity Studio
npx sanity deploy --workspace=studio
```

## Environment Setup

### Frontend Environment Variables
Required in `frontend/.env.local`:
- `NEXT_PUBLIC_SANITY_PROJECT_ID` - Sanity project ID
- `NEXT_PUBLIC_SANITY_DATASET` - Dataset (usually "production")  
- `SANITY_API_READ_TOKEN` - Read token for API access

Optional:
- `NEXT_PUBLIC_SANITY_API_VERSION` - API version (defaults to 2024-10-28)
- `NEXT_PUBLIC_SANITY_STUDIO_URL` - Studio URL (defaults to localhost:3333)

### Studio Environment Variables  
Required in `studio/.env.local`:
- `SANITY_STUDIO_PROJECT_ID` - Sanity project ID
- `SANITY_STUDIO_DATASET` - Dataset name

Optional:
- `SANITY_STUDIO_PREVIEW_URL` - Preview URL (defaults to localhost:3000)
- `SANITY_STUDIO_STUDIO_HOST` - Studio host for deployment

## Content Architecture

### Document Types
- **Settings** (singleton) - Global site configuration
- **Page** - Dynamic pages with page builder
- **Post** - Blog posts with rich content
- **Person** - Author/person profiles

### Page Builder Components
- **Call to Action** - CTA sections with configurable links
- **Info Section** - Rich text content blocks
- **Block Content** - Portable text with custom blocks

### Schema Structure
Schema types are organized in `studio/src/schemaTypes/`:
- `documents/` - Main document types (page, post, person)
- `objects/` - Reusable schema objects (callToAction, infoSection, blockContent, link)
- `singletons/` - Single-instance documents (settings)

## Key Development Patterns

### GROQ Queries
All queries are defined in `frontend/sanity/lib/queries.ts` using `defineQuery()` for type safety. Queries use fragments for reusable field selections and handle draft/published content states.

### Visual Editing Integration
The project uses Sanity's Presentation Tool for real-time visual editing:
- `resolveHref()` function maps document types to frontend routes
- Location resolvers define where content appears in the app
- Draft mode enables live preview functionality

### Live Content Updates
Uses Sanity Live Content API (`SanityLive` component) for real-time content updates without page refreshes.

### Type Generation
TypeScript types are automatically generated from Sanity schemas:
- Run `typegen` before development/build
- Types are generated in `sanity.types.ts`
- Ensures type safety between CMS and frontend

## Common Development Workflows

### Adding New Content Types
1. Create schema in `studio/src/schemaTypes/`
2. Add to schema export in `index.ts`
3. Create GROQ queries in `frontend/sanity/lib/queries.ts`
4. Add route handling in `sanity.config.ts` resolvers
5. Generate types with `npm run typegen --workspace=frontend`

### Debugging Visual Editing
- Check `resolveHref()` function for correct route mapping  
- Verify document resolvers in `sanity.config.ts`
- Ensure draft mode is enabled via `/api/draft-mode/enable`
- Check `SANITY_STUDIO_PREVIEW_URL` environment variable
