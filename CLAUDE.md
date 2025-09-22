# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Root-level commands:
- `yarn setup` - Initialize environment files and install dependencies for all services
- `yarn setup:envs` - Copy example environment files
- `yarn prisma:setup` - Generate Prisma client, run migrations, and seed database
- `yarn prisma:generate` - Generate Prisma client
- `yarn prisma:migrate` - Run database migrations
- `yarn prisma:reset` - Reset database and re-run migrations
- `yarn dev:server` - Start backend server in development mode
- `yarn dev:frontend` - Start frontend in development mode
- `yarn dev:collector` - Start document collector in development mode
- `yarn dev:all` - Start all three services concurrently
- `yarn lint` - Run prettier on all three services
- `yarn test` - Run Jest tests
- `yarn verify:translations` - Verify translation file integrity
- `yarn normalize:translations` - Normalize translation files

### Service-specific commands:
**Server (`/server`):**
- `yarn dev` - Start with nodemon in development mode
- `yarn start` - Start in production mode
- `yarn lint` - Format code with prettier
- `yarn swagger` - Generate Swagger documentation

**Frontend (`/frontend`):**
- `yarn dev` - Start Vite dev server
- `yarn build` - Build for production
- `yarn preview` - Preview production build
- `yarn lint` - Format code with prettier

**Collector (`/collector`):**
- `yarn dev` - Start with nodemon in development mode
- `yarn start` - Start in production mode
- `yarn lint` - Format code with prettier

## Architecture Overview

### Three-Service Architecture
AnythingLLM is a **monorepo** with three independent Node.js services:

1. **Server** (`/server`) - Main API and business logic
2. **Frontend** (`/frontend`) - React/Vite web application
3. **Collector** (`/collector`) - Document processing service

### Server Architecture (`/server`)

**Core Structure:**
- `index.js` - Express app entry point, routes all endpoint modules
- `/endpoints/` - API route handlers organized by feature
- `/models/` - Prisma database models and business logic
- `/utils/` - Shared utilities, providers, and helpers
- `/prisma/` - Database schema and migrations

**Key Subsystems:**
- **AI Providers** (`/utils/AiProviders/`) - Abstractions for 25+ LLM providers (OpenAI, Anthropic, Ollama, etc.)
- **Vector Databases** (`/utils/VectorDBProviders/`) - Abstractions for vector stores (LanceDB, Pinecone, Chroma, etc.)
- **Document Processing** - Integration with collector service for file ingestion
- **Agent System** (`/utils/agents/`) - AI agent implementations and tools
- **WebSocket Support** - Real-time chat via `/endpoints/agentWebsocket.js`
- **Multi-user System** - Workspace-based permissions and user management

**Database:**
- Uses **Prisma ORM** with SQLite (development) or PostgreSQL/MySQL (production)
- Key entities: `workspaces`, `users`, `workspace_documents`, `workspace_chats`
- Workspaces are containers that isolate documents and conversations

### Frontend Architecture (`/frontend`)

**Technology Stack:**
- **React 18** with functional components and hooks
- **Vite** for build tooling and development server
- **Tailwind CSS** for styling
- **React Router** for client-side routing
- **i18next** for internationalization (20+ languages)

**Structure:**
- `/src/components/` - Reusable UI components
- `/src/pages/` - Route-level page components
- `/src/utils/` - Frontend utilities and API clients
- `/src/locales/` - Translation files for multiple languages

### Collector Architecture (`/collector`)

**Purpose:** Processes and converts documents into vector-ready text chunks

**Capabilities:**
- **File Processing**: PDF, DOCX, TXT, EPUB, XLSX, images (via OCR)
- **Web Scraping**: URL content extraction with Puppeteer
- **YouTube Integration**: Video transcript extraction
- **OCR**: Image-to-text conversion with Tesseract.js
- **Audio Transcription**: Whisper integration for speech-to-text

**Structure:**
- `/processSingleFile/` - Individual file processing logic
- `/processLink/` - Web content scraping
- `/utils/` - Shared processing utilities
- `/extensions/` - Additional processing capabilities

### Inter-Service Communication

**Development Flow:**
1. **Frontend** → **Server** (API calls for chat, workspace management)
2. **Server** → **Collector** (document processing requests)
3. **Server** → **Vector DB** (embedding storage/retrieval)
4. **Server** → **LLM Provider** (chat completions)

**Production Deployment:**
- Services can be deployed independently or together
- Docker support for containerized deployment
- Cloud deployment templates for AWS, GCP, DigitalOcean

### Key Concepts

**Workspaces:**
- Primary organizational unit containing documents and conversations
- Each workspace has isolated context and can use different LLM settings
- Support for multi-user access with permission controls

**Document Pipeline:**
1. Upload → Collector processing → Text chunking → Vector embedding → Storage
2. Chat → Vector similarity search → Context retrieval → LLM completion

**Agent System:**
- Custom AI agents with tool capabilities (web browsing, calculations)
- Agent flows for no-code agent building
- WebSocket-based real-time agent interactions

### Development Environment Setup

**Prerequisites:**
- Node.js >=18
- Yarn package manager

**Required Environment Files:**
- `server/.env.development` - Server configuration (REQUIRED)
- `frontend/.env` - Frontend configuration
- `collector/.env` - Collector configuration

**Database Setup:**
- Default: SQLite for development (`server/storage/anythingllm.db`)
- Production: PostgreSQL or MySQL (update `server/prisma/schema.prisma`)

### Testing

**Test Framework:** Jest
- Tests located in `__tests__` directories within each service
- Run all tests: `yarn test` from root
- Server tests focus on utilities and API logic
- Limited frontend testing currently implemented

### Code Style

**Formatting:** Prettier with shared configuration
- `.prettierignore` at root level defines ignored files
- Each service has `yarn lint` command for formatting
- No ESLint currently configured

### Key Files to Understand

- **Root**: `package.json` (scripts), `README.md` (deployment)
- **Server**: `index.js` (routing), `prisma/schema.prisma` (data model)
- **Frontend**: `src/main.jsx` (app entry), `src/App.jsx` (routing)
- **Collector**: `index.js` (processing server), `/utils/` (processing logic)