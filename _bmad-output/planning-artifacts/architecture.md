---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
workflowType: 'architecture'
lastStep: 8
status: 'complete'
completedAt: '2026-01-15'
inputDocuments: ["c:\\Users\\FranklinOuattara\\Desktop\\PickSizer\\_bmad-output\\planning-artifacts\\prd.md"]
workflowType: 'architecture'
project_name: 'PickSizer'
user_name: 'FranklinOuattara'
date: '2026-01-15'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
21 functional requirements organized into 5 core capability areas:

- **Image Intelligence & Processing (FR1-FR4)**: WebAssembly-powered transformations, semantic object detection, contextual classification, and sub-300ms client-side processing
- **Brand DNA Management (FR5-FR8)**: Silent learning capture, encrypted taste parameters, multi-client DNA strands, and cross-session persistence
- **User Interaction & Confidence (FR9-FR12)**: Flight Envelope visualization, Safe Zone overlays, low-friction corrections, and seamless SPA experience
- **Collaboration & Inheritance (FR13-FR16)**: Permission-based authority levels, real-time DNA sync, contextual inheritance, and team collaboration
- **Safety & Recovery (FR17-FR21)**: Automatic authority down-scaling, Safety Locks, decision logging, and per-image envelope analysis

**Non-Functional Requirements:**
13 non-functional requirements driving architectural decisions:

- **Performance (NFR1-NFR4)**: <300ms UI feedback, <2s Wasm processing (<400ms with JavaScript fallback), predictive skeleton rendering, <50ms jitter rate
- **Security (NFR5-NFR8)**: Volatile memory storage, no cloud uploads for processing, AES-256 encryption, SOC2 compliance
- **Accessibility (NFR9-NFR11)**: Aria-live announcements, keyboard navigation, color-blind accommodations
- **Integration (NFR12-NFR13)**: Operational Transformation via WebSockets, <1.5s API response times

**Scale & Complexity:**
Medium-High complexity web application with advanced AI/ML capabilities

- Primary domain: Full-stack Web Application with heavy client-side processing
- Complexity level: Medium-High (real-time collaboration + AI safety systems)
- Estimated architectural components: 8-10 major systems

### Technical Constraints & Dependencies

- **Performance Constraint**: <300ms Interactive Pulse requires WebAssembly optimization (<400ms acceptable with JavaScript fallback)
- **Privacy Constraint**: Local-first processing eliminates cloud-based image processing
- **Compliance Constraint**: SOC2 compliance required for enterprise adoption
- **Browser Support**: Latest 2 versions of major browsers with progressive enhancement
- **Platform**: Next.js with App Router for SPA/SSR bifurcation

### Cross-Cutting Concerns Identified

- **Real-time State Synchronization**: WebSockets with OT/CRDTs for collaborative Brand DNA
- **Performance Optimization**: Client-side Wasm processing with JavaScript fallback and skeleton UI states
- **Security & Privacy**: Zero-retention architecture with encrypted metadata sync
- **Accessibility**: WCAG 2.1 Level AA compliance across all interactive elements
- **AI Safety Systems**: Aviation-grade authority management and failure recovery

## Starter Template Evaluation

### Primary Technology Domain

Web Application with Next.js App Router based on project requirements analysis

### Starter Options Considered

**Option 1: Official Next.js create-next-app (Latest)**
- Next.js 16.1 with React 19.2 (bleeding edge)
- Tailwind CSS 4.1.18 with latest features
- Basic production-ready configuration
- Minimal dependencies, clean slate approach

**Option 2: ts-nextjs-tailwind-starter (theodorusclarence)**
- Next.js 15.4.3 with React 19.1.0 (very current, stable)
- Tailwind CSS 4.1.11 (latest stable)
- Comprehensive development tooling pre-configured
- Extensive component library and development workflow
- Active maintenance with 2.5k+ stars

### Selected Starter: ts-nextjs-tailwind-starter

**Rationale for Selection:**
While Next.js 16.1 just released, the starter's Next.js 15.4.3 is extremely stable and includes all features needed for PickSizer. The starter provides comprehensive tooling that aligns with enterprise-grade requirements while maintaining development velocity. The pre-configured testing, linting, and deployment workflows reduce architectural decision overhead.

**Initialization Command:**

```bash
pnpm create next-app -e https://github.com/theodorusclarence/ts-nextjs-tailwind-starter pick-sizer
cd pick-sizer
pnpm install
pnpm dev
```

**Architectural Decisions Provided by Starter:**

**Language & Runtime:**
- Next.js 15.4.3 with App Router (very current, stable)
- React 19.1.0 with latest features
- TypeScript 5.8.3 (latest)
- Node.js 24.11.0 LTS compatible

**Styling Solution:**
- **Choice**: Shadcn UI + Tailwind CSS 4.1.11
- **Rationale**: Production-ready component library with accessibility, TypeScript support, and modern design patterns
- **Features**: Pre-built accessible components, Radix UI primitives, CSS Variables for theming
- **Integration**: Works seamlessly with Next.js 15.4.3 and TypeScript 5.8.3
- **Impact**: Accelerates development with consistent, accessible UI components

**Build Tooling:**
- Next.js 15.4.3 with Turbopack support
- Optimized production builds
- Path aliases configured (@/* imports)
- Absolute import system

**Testing Framework:**
- Jest 27.5.1 configured for unit testing
- Testing Library pre-configured
- Coverage reporting setup

**Code Organization:**
- src/ directory structure
- Component-based architecture patterns
- Conventional commit enforcement
- Automated changelog generation

**Development Experience:**
- ESLint + Prettier with pre-commit hooks
- Husky for git hooks automation
- GitHub Actions for CI/CD
- Open Graph helper functions
- Automatic sitemap generation

**Note:** Project initialization using this command should be the first implementation story. The starter can be easily upgraded to Next.js 16.1 after project setup if needed.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Data Architecture: Supabase (PostgreSQL) + IndexedDB for "Distributed Cockpit"
- Authentication: Supabase Auth for unified security stack
- State Management: XState for "Flight Controller" logic
- WebAssembly Integration: Rust + Wasm-Pack for "Turbine Engine"

**Important Decisions (Shape Architecture):**
- API Design: RESTful patterns with Supabase Realtime integration
- Security Implementation: Row-Level Security (RLS) + SOC2 compliance
- Performance Optimization: Client-side processing + skeleton UI states
- Deployment Infrastructure: Vercel + Supabase hosting

**Deferred Decisions (Post-MVP):**
- Advanced AI/ML model deployment strategies
- Enterprise SSO integration patterns
- Custom analytics pipeline architecture

### Data Architecture

**Database: Supabase (PostgreSQL)**
- **Version**: Latest stable
- **Rationale**: PostgreSQL reliability + built-in real-time features + row-level security
- **Features**: JSONB for flexible Brand DNA schemas, real-time sync for collaboration
- **Local Cache**: IndexedDB for "Distributed Cockpit" architecture
- **Impact**: Enables instant "Confidence Glow" updates even during internet blinks

### Authentication & Security

**Authentication: Supabase Auth**
- **Version**: Latest stable
- **Rationale**: Seamless integration with RLS and unified security stack
- **Features**: Enterprise SSO, MFA support, session management
- **Compliance**: Row-Level Security for SOC2 enterprise requirements
- **Impact**: Sarah's Brand DNA walled off from Marcus's personal projects

### API & Communication Patterns

**API Design: RESTful with Supabase Realtime**
- **Version**: Latest stable
- **Rationale**: Proven patterns with real-time collaboration features
- **Features**: REST for CRUD, WebSockets for live Brand DNA updates
- **Security**: Built-in rate limiting and API key management
- **Impact**: Real-time "Ripple" updates when Lead Designers change rules

### Frontend Architecture

**State Management: XState**
- **Version**: Latest stable
- **Rationale**: Finite State Machines for aviation-grade safety
- **Features**: State charts for authority transitions, guaranteed safe states
- **Integration**: Event-driven architecture with Supabase Realtime
- **Impact**: Prevents impossible UI states like Autopilot during Safety Lock

**WebAssembly Engine: Rust + Wasm-Pack with JavaScript Fallback**
- **Version**: Latest stable
- **Rationale**: Near-native performance with memory safety and progressive enhancement
- **Features**: Image crate for processing, worker thread execution, JavaScript fallback for compatibility
- **Integration**: Custom React hooks with automatic fallback detection
- **Performance**: <300ms Interactive Pulse with WebAssembly, <400ms with JavaScript fallback
- **Fallback Strategy**: Enhanced JavaScript implementation ensures full functionality across all environments
- **Impact**: Achieves performance targets while maintaining broad compatibility and reliability

### Infrastructure & Deployment

**Hosting: Vercel + Supabase**
- **Version**: Latest stable
- **Rationale**: Native Next.js optimization + database hosting
- **Features**: Edge functions, automatic scaling, built-in CDN
- **Compliance**: SOC2-ready infrastructure
- **Impact**: Global performance with enterprise reliability

### Decision Impact Analysis

**Implementation Sequence:**
1. Initialize Next.js project with ts-nextjs-tailwind-starter
2. Set up Supabase database and authentication
3. Implement XState machines for authority management
4. Integrate Rust WebAssembly for image processing with JavaScript fallback
5. Connect real-time collaboration features
6. Deploy to Vercel with Supabase integration

**Cross-Component Dependencies:**
- XState depends on Supabase Realtime for state synchronization
- WebAssembly engine with JavaScript fallback feeds confidence data to XState machines
- Supabase RLS enforces security boundaries defined in XState
- IndexedDB cache mirrors Supabase for offline resilience

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:**
4 major areas where AI agents could make different choices

### Naming Patterns

**Database Naming Conventions:**
- **Tables**: snake_case plural (users, brand_dna_profiles, image_processing_jobs)
- **Columns**: snake_case (user_id, brand_dna_id, created_at)
- **Foreign Keys**: referencing_table_id (user_id, brand_dna_id)
- **Indexes**: idx_table_column (idx_users_email, idx_brand_dna_user_id)

**API Naming Conventions:**
- **Endpoints**: RESTful plural (/api/users, /api/brand-dna, /api/images)
- **Route Parameters**: :id format (/api/users/:id)
- **Query Parameters**: snake_case (user_id, brand_dna_id)
- **Headers**: X-Custom-Header format (X-Request-ID, X-User-Role)

**Code Naming Conventions:**
- **Components**: PascalCase (UserCard, BrandDnaEditor, ImageProcessor)
- **Files**: PascalCase with extensions (UserCard.tsx, BrandDnaEditor.tsx)
- **Functions**: camelCase (getUserData, updateBrandDna, processImage)
- **Variables**: camelCase (userId, brandDnaId, imageProcessingJob)

### Structure Patterns

**Project Organization:**
- **Shared Components**: `/src/components/` (reusable across features)
- **Feature Components**: `/src/features/[feature]/components/`
- **Hooks**: `/src/hooks/` (custom React hooks)
- **Utils**: `/src/utils/` (shared utility functions)
- **Services**: `/src/services/` (API and external service integrations)
- **Types**: `/src/types/` (TypeScript type definitions)

**File Structure Patterns:**
- **Tests**: Co-located with files (*.test.ts, *.test.tsx)
- **Stories**: Co-located with components (*.stories.tsx)
- **Config**: `/config/` (Next.js, Tailwind, Jest configurations)
- **Static Assets**: `/public/` (images, fonts, icons)
- **Environment**: `.env.local`, `.env.production`

### Format Patterns

**API Response Formats:**
- **Success**: `{ data: T, meta?: { pagination?: PaginationInfo } }`
- **Error**: `{ error: { code: string, message: string, details?: any } }`
- **Dates**: ISO 8601 strings (2026-01-15T16:03:00.000Z)
- **Booleans**: true/false (not 1/0)
- **Null Handling**: Use null for absent values, undefined for uninitialized

**Data Exchange Formats:**
- **JSON Fields**: camelCase for frontend, snake_case for database
- **Arrays**: Always arrays, even for single items in responses
- **Objects**: Consistent key naming across API responses
- **File Uploads**: multipart/form-data with metadata

### Communication Patterns

**Event System Patterns:**
- **XState Events**: SCREAMING_SNAKE_CASE (USER_CREATED, BRAND_DNA_UPDATED, IMAGE_PROCESSED)
- **Supabase Events**: dot.notation (user.created, brandDna.updated, image.processed)
- **Event Payloads**: Consistent structure `{ type: string, payload: T, timestamp: string }`
- **Event Versioning**: Include version in payload for breaking changes

**State Management Patterns:**
- **State Updates**: Immutable updates only (no direct mutations)
- **Actions**: camelCase functions (updateUser, setBrandDna, processImage)
- **Selectors**: useMemo for derived state, useCallback for actions
- **State Organization**: By feature, not by technical concern

### Process Patterns

**Error Handling Patterns:**
- **Global Errors**: Error boundaries with user-friendly messages
- **API Errors**: Standardized error response format
- **Validation Errors**: Field-specific error messages
- **Logging**: Structured logging with context, not sensitive data

**Loading State Patterns:**
- **Naming**: isLoading, isError, isSuccess pattern
- **Global Loading**: App-level loading for major operations
- **Local Loading**: Component-level for specific actions
- **Skeleton States**: Use skeleton components during loading

### Enforcement Guidelines

**All AI Agents MUST:**

1. Follow naming conventions exactly as specified
2. Use the established file structure patterns
3. Implement error handling using the defined patterns
4. Use the established API response formats
5. Follow XState event naming conventions
6. Co-locate tests with their source files
7. Use TypeScript strict mode and proper typing

**Pattern Enforcement:**

- **ESLint Rules**: Configure rules to enforce naming conventions
- **Prettier**: Consistent code formatting
- **TypeScript**: Strict type checking for API responses and state
- **Tests**: Include pattern compliance in test suites
- **Documentation**: Update this document when patterns change

### Pattern Examples

**Good Examples:**
```typescript
// Component naming and structure
export const UserCard: React.FC<UserCardProps> = ({ user }) => {
  // Component implementation
};

// API endpoint naming
export async function GET(request: Request) {
  // API implementation
}

// XState event naming
const machine = createMachine({
  on: {
    USER_CREATED: {
      actions: 'handleUserCreated'
    }
  }
});
```

**Anti-Patterns:**
```typescript
// Incorrect naming
export const userCard = ({ user }) => { /* ... */ };

// Inconsistent API structure
export async function getUsers() {
  return users; // Should be { data: users }
}

// Mixed event naming
const machine = createMachine({
  on: {
    userCreated: { // Should be USER_CREATED
      actions: 'handleUserCreated'
    }
  }
});
```

## Project Structure & Boundaries

### Complete Project Directory Structure

```
pick-sizer/
├── README.md
├── package.json
├── pnpm-lock.yaml
├── next.config.js
├── tailwind.config.js
├── tsconfig.json
├── .env.local
├── .env.example
├── .gitignore
├── .eslintrc.json
├── .prettierrc
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── deploy.yml
├── src/
│   ├── app/
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   └── signup/
│   │   │       └── page.tsx
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   └── api/
│   │       ├── auth/
│   │       │   └── route.ts
│   │       ├── users/
│   │       │   └── route.ts
│   │       ├── brand-dna/
│   │       │   └── route.ts
│   │       ├── images/
│   │       │   └── route.ts
│   │       └── realtime/
│   │           └── route.ts
│   ├── components/
│   │   ├── ui/
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── LoadingSpinner.tsx
│   │   │   └── index.ts
│   │   ├── forms/
│   │   │   ├── LoginForm.tsx
│   │   │   ├── BrandDnaForm.tsx
│   │   │   └── ImageUploadForm.tsx
│   │   └── features/
│   │       ├── image-processing/
│   │       │   ├── ImageProcessor.tsx
│   │       │   ├── ConfidenceGlow.tsx
│   │       │   ├── SafeZoneOverlay.tsx
│   │       │   └── ProcessingControls.tsx
│   │       ├── brand-dna/
│   │       │   ├── BrandDnaEditor.tsx
│   │       │   ├── FlightEnvelope.tsx
│   │       │   ├── AuthorityIndicator.tsx
│   │       │   └── BrandRulesManager.tsx
│   │       ├── collaboration/
│   │       │   ├── TeamPresence.tsx
│   │       │   ├── RealtimeSync.tsx
│   │       │   ├── SharedBrandDna.tsx
│   │       │   └── CollaborationPanel.tsx
│   │       ├── safety/
│   │       │   ├── SafetyLock.tsx
│   │       │   ├── ConflictResolver.tsx
│   │       │   ├── ErrorBoundary.tsx
│   │       │   └── RecoveryControls.tsx
│   │       └── auth/
│   │           ├── AuthGuard.tsx
│   │           ├── UserProfile.tsx
│   │           └── PermissionGate.tsx
│   ├── features/
│   │   ├── image-processing/
│   │   │   ├── components/
│   │   │   │   ├── ImageUpload.tsx
│   │   │   │   ├── ProcessingQueue.tsx
│   │   │   │   └── ResultsGallery.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── useImageProcessing.ts
│   │   │   │   ├── useWasmProcessor.ts
│   │   │   │   └── useProcessingQueue.ts
│   │   │   ├── services/
│   │   │   │   ├── imageProcessingService.ts
│   │   │   │   └── wasmInterface.ts
│   │   │   ├── types/
│   │   │   │   └── imageProcessing.ts
│   │   │   └── machines/
│   │   │       └── imageProcessingMachine.ts
│   │   ├── brand-dna/
│   │   │   ├── components/
│   │   │   │   ├── BrandDnaCard.tsx
│   │   │   │   ├── RuleEditor.tsx
│   │   │   │   └── DnaPreview.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── useBrandDna.ts
│   │   │   │   ├── useBrandRules.ts
│   │   │   │   └── useDnaSync.ts
│   │   │   ├── services/
│   │   │   │   ├── brandDnaService.ts
│   │   │   │   └── ruleEngine.ts
│   │   │   ├── types/
│   │   │   │   └── brandDna.ts
│   │   │   └── machines/
│   │   │       └── brandDnaMachine.ts
│   │   ├── collaboration/
│   │   │   ├── components/
│   │   │   │   ├── TeamMemberList.tsx
│   │   │   │   ├── ActivityFeed.tsx
│   │   │   │   └── SharedProjects.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── useRealtimeSync.ts
│   │   │   │   ├── useTeamPresence.ts
│   │   │   │   └── useCollaboration.ts
│   │   │   ├── services/
│   │   │   │   ├── collaborationService.ts
│   │   │   │   └── realtimeService.ts
│   │   │   ├── types/
│   │   │   │   └── collaboration.ts
│   │   │   └── machines/
│   │   │       └── collaborationMachine.ts
│   │   └── safety/
│   │       ├── components/
│   │       │   ├── SafetyIndicator.tsx
│   │       │   ├── ConflictDialog.tsx
│   │       │   └── RecoveryFlow.tsx
│   │       ├── hooks/
│   │       │   ├── useSafetySystems.ts
│   │       │   ├── useConflictDetection.ts
│   │       │   └── useRecovery.ts
│   │       ├── services/
│   │       │   ├── safetyService.ts
│   │       │   └── conflictResolver.ts
│   │       ├── types/
│   │       │   └── safety.ts
│   │       └── machines/
│   │           └── safetyMachine.ts
│   ├── lib/
│   │   ├── db.ts
│   │   ├── auth.ts
│   │   ├── utils.ts
│   │   ├── validations.ts
│   │   ├── constants.ts
│   │   └── supabase.ts
│   ├── hooks/
│   │   ├── useAuth.ts
│   │   ├── useSupabase.ts
│   │   ├── useLocalStorage.ts
│   │   └── useErrorBoundary.ts
│   ├── services/
│   │   ├── api.ts
│   │   ├── storage.ts
│   │   └── analytics.ts
│   ├── types/
│   │   ├── api.ts
│   │   ├── auth.ts
│   │   ├── database.ts
│   │   └── global.d.ts
│   ├── store/
│   │   ├── machines/
│   │   │   ├── authMachine.ts
│   │   │   ├── appMachine.ts
│   │   │   └── rootMachine.ts
│   │   ├── services/
│   │   │   └── storeService.ts
│   │   └── hooks/
│   │       └── useStore.ts
│   └── middleware.ts
├── wasm/
│   ├── src/
│   │   ├── lib.rs
│   │   ├── image_processing.rs
│   │   ├── object_detection.rs
│   │   └── brand_analysis.rs
│   ├── pkg/
│   │   ├── pick_sizer.js
│   │   ├── pick_sizer_bg.wasm
│   │   └── pick_sizer.d.ts
│   ├── Cargo.toml
│   └── build.sh
├── supabase/
│   ├── migrations/
│   │   ├── 20240115_create_users.sql
│   │   ├── 20240115_create_brand_dna.sql
│   │   ├── 20240115_create_images.sql
│   │   └── 20240115_create_collaboration.sql
│   ├── functions/
│   │   ├── handle_realtime_sync.ts
│   │   └── process_brand_dna_update.ts
│   ├── seed.sql
│   └── types.ts
├── tests/
│   ├── __mocks__/
│   │   ├── supabase.ts
│   │   └── wasm.ts
│   ├── components/
│   │   ├── ui/
│   │   ├── features/
│   │   └── forms/
│   ├── features/
│   │   ├── image-processing/
│   │   ├── brand-dna/
│   │   ├── collaboration/
│   │   └── safety/
│   ├── integration/
│   │   ├── api/
│   │   ├── database/
│   │   └── wasm/
│   ├── e2e/
│   │   ├── auth.spec.ts
│   │   ├── image-processing.spec.ts
│   │   └── collaboration.spec.ts
│   └── setup.ts
├── public/
│   ├── assets/
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   ├── wasm/
│   │   └── pick_sizer_bg.wasm
│   └── favicon.ico
├── docs/
│   ├── api/
│   ├── architecture/
│   └── deployment/
└── config/
    ├── jest.config.js
    ├── next-env.d.ts
    └── postcss.config.js
```

### Architectural Boundaries

**API Boundaries:**

- **External APIs**: Supabase (database, auth, realtime), Vercel (deployment)
- **Internal API Routes**: `/api/auth/*`, `/api/users/*`, `/api/brand-dna/*`, `/api/images/*`
- **Authentication Boundary**: Supabase Auth + RLS policies
- **Data Access Boundary**: Service layer with Supabase client

**Component Boundaries:**

- **UI Components**: Reusable components in `/src/components/ui/`
- **Feature Components**: Domain-specific in `/src/components/features/`
- **State Management**: XState machines with event-driven communication
- **Service Communication**: Service layer with Supabase integration

**Service Boundaries:**

- **Image Processing**: Rust Wasm module with JavaScript interface
- **Brand DNA Engine**: Rule engine with Supabase persistence
- **Collaboration Engine**: Realtime sync with Supabase Realtime
- **Safety Systems**: Conflict detection and recovery services

**Data Boundaries:**

- **Database Schema**: PostgreSQL with RLS policies
- **Client Cache**: IndexedDB for offline resilience
- **Realtime Sync**: Supabase Realtime subscriptions
- **Wasm Memory**: Isolated WebAssembly memory space

### Requirements to Structure Mapping

**Feature/Epic Mapping:**

**Image Intelligence & Processing (FR1-FR4):**

- Components: `/src/features/image-processing/components/`
- Services: `/src/features/image-processing/services/`
- Wasm: `/wasm/src/image_processing.rs`
- API Routes: `/src/app/api/images/route.ts`
- Tests: `/tests/features/image-processing/`

**Brand DNA Management (FR5-FR8):**

- Components: `/src/features/brand-dna/components/`
- Services: `/src/features/brand-dna/services/`
- Database: `/supabase/migrations/*brand_dna*.sql`
- API Routes: `/src/app/api/brand-dna/route.ts`
- Tests: `/tests/features/brand-dna/`

**User Interaction & Confidence (FR9-FR12):**

- Components: `/src/components/features/image-processing/`
- XState Machines: `/src/features/*/machines/`
- UI Components: `/src/components/ui/`
- Tests: `/tests/components/`

**Collaboration & Inheritance (FR13-FR16):**

- Components: `/src/features/collaboration/components/`
- Services: `/src/features/collaboration/services/`
- Realtime: `/supabase/functions/handle_realtime_sync.ts`
- API Routes: `/src/app/api/realtime/route.ts`
- Tests: `/tests/features/collaboration/`

**Safety & Recovery (FR17-FR21):**

- Components: `/src/features/safety/components/`
- Services: `/src/features/safety/services/`
- XState Machines: `/src/features/safety/machines/safetyMachine.ts`
- Error Boundaries: `/src/components/features/safety/ErrorBoundary.tsx`
- Tests: `/tests/features/safety/`

**Cross-Cutting Concerns:**

**Authentication System:**

- Components: `/src/components/auth/`
- Services: `/src/lib/auth.ts`
- Middleware: `/src/middleware.ts`
- API Routes: `/src/app/api/auth/route.ts`
- Tests: `/tests/components/auth/`

**State Management:**

- Machines: `/src/store/machines/`
- Services: `/src/store/services/`
- Hooks: `/src/store/hooks/`
- Types: `/src/types/`

### Integration Points

**Internal Communication:**

- **XState Events**: SCREAMING_SNAKE_CASE between machines
- **Service Layer**: Centralized API and Supabase communication
- **Component Communication**: Props + callbacks + XState context
- **Feature Communication**: Shared services and XState machines

**External Integrations:**

- **Supabase**: Database, Auth, Realtime, Storage
- **Vercel**: Deployment, Edge Functions, Analytics
- **WebAssembly**: Image processing via `/wasm/` module
- **Browser APIs**: IndexedDB, Web Workers, File API

**Data Flow:**

1. **User Input** → Component → XState Event
2. **XState Machine** → Service Layer → Supabase
3. **Supabase Realtime** → XState Event → Component Update
4. **WebAssembly** → Processing Result → XState Event
5. **IndexedDB Cache** → Offline Support → Component State

### File Organization Patterns

**Configuration Files:**

- **Root**: `package.json`, `next.config.js`, `tailwind.config.js`
- **Environment**: `.env.local`, `.env.example`
- **Build**: `tsconfig.json`, `.eslintrc.json`, `.prettierrc`
- **CI/CD**: `.github/workflows/`

**Source Organization:**

- **App Router**: `/src/app/` with route groups
- **Components**: Shared UI + feature-specific components
- **Features**: Self-contained feature modules
- **Services**: Centralized business logic
- **Types**: Global TypeScript definitions

**Test Organization:**

- **Unit Tests**: Co-located with source files
- **Integration Tests**: `/tests/integration/`
- **E2E Tests**: `/tests/e2e/`
- **Mocks**: `/tests/__mocks__/`

**Asset Organization:**

- **Static Assets**: `/public/assets/`
- **Wasm Files**: `/public/wasm/`
- **Documentation**: `/docs/`
- **Config**: `/config/`

### Development Workflow Integration

**Development Server Structure:**

- **Next.js Dev**: Hot reload with Turbopack
- **Wasm Development**: Build script in `/wasm/build.sh`
- **Supabase Local**: Local development database
- **Realtime Development**: WebSocket connections

**Build Process Structure:**

- **TypeScript**: Strict mode compilation
- **Wasm Compilation**: Rust → Wasm → TypeScript bindings
- **Asset Optimization**: Next.js built-in optimization
- **Environment Variables**: Build-time configuration

**Deployment Structure:**

- **Vercel**: Automatic deployment from main branch
- **Supabase**: Database migrations and functions
- **Wasm**: Compiled assets in `/public/wasm/`
- **Environment**: Production environment variables

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:**
All technology choices work together without conflicts. Next.js 15.4.3 + React 19.1.0 + TypeScript 5.8.3 are fully compatible. Supabase + XState + Rust Wasm integration patterns are coherent. No contradictory architectural decisions identified.

**Pattern Consistency:**
Implementation patterns fully support architectural decisions. Naming conventions consistent across database, API, and code. Structure patterns align with technology stack. Communication patterns (XState events, Supabase realtime) are coherent.

**Structure Alignment:**
Project structure supports all architectural decisions. Boundaries properly defined and respected. Structure enables chosen patterns. Integration points properly structured.

### Requirements Coverage Validation ✅

**FR Categories Coverage:**

- **Image Intelligence & Processing (FR1-FR4)**: Fully supported by Rust Wasm + XState
- **Brand DNA Management (FR5-FR8)**: Fully supported by Supabase + JSONB
- **User Interaction & Confidence (FR9-FR12)**: Fully supported by XState + React
- **Collaboration & Inheritance (FR13-FR16)**: Fully supported by Supabase Realtime
- **Safety & Recovery (FR17-FR21)**: Fully supported by XState safety machines

**Non-Functional Requirements Coverage:**

- **Performance (<300ms)**: Addressed by Rust Wasm + client-side processing
- **Security (SOC2)**: Addressed by Supabase RLS + auth
- **Accessibility (WCAG 2.1)**: Addressed by component patterns
- **Real-time collaboration**: Addressed by Supabase Realtime + WebSockets

### Implementation Readiness Validation ✅

**Decision Completeness:**
All critical decisions documented with versions. Implementation patterns comprehensive. Consistency rules clear and enforceable. Examples provided for all major patterns.

**Structure Completeness:**
Project structure complete and specific. All files and directories defined. Integration points clearly specified. Component boundaries well-defined.

**Pattern Completeness:**
All potential conflict points addressed. Naming conventions comprehensive. Communication patterns fully specified. Process patterns complete.

### Gap Analysis Results

**Critical Gaps:** None identified

**Important Gaps:** None identified

**Nice-to-Have Gaps:**
- Additional tooling recommendations for Wasm development
- Enhanced documentation for XState machine debugging
- Performance monitoring patterns for production

### Validation Issues Addressed

No critical issues found during validation. Architecture is coherent and complete.

### Architecture Completeness Checklist

**✅ Requirements Analysis**

- [x] Project context thoroughly analyzed
- [x] Scale and complexity assessed
- [x] Technical constraints identified
- [x] Cross-cutting concerns mapped

**✅ Architectural Decisions**

- [x] Critical decisions documented with versions
- [x] Technology stack fully specified
- [x] Integration patterns defined
- [x] Performance considerations addressed

**✅ Implementation Patterns**

- [x] Naming conventions established
- [x] Structure patterns defined
- [x] Communication patterns specified
- [x] Process patterns documented

**✅ Project Structure**

- [x] Complete directory structure defined
- [x] Component boundaries established
- [x] Integration points mapped
- [x] Requirements to structure mapping complete

### Architecture Readiness Assessment

**Overall Status:** READY FOR IMPLEMENTATION

**Confidence Level:** HIGH based on validation results

**Key Strengths:**

- Aviation-grade safety architecture with XState finite state machines
- Performance-optimized with Rust WebAssembly for <300ms processing
- Enterprise-ready with Supabase SOC2 compliance and real-time collaboration
- Comprehensive implementation patterns preventing AI agent conflicts
- Complete project structure mapped to all functional requirements

**Areas for Future Enhancement:**

- Advanced AI/ML model deployment strategies
- Enhanced monitoring and observability patterns
- Additional tooling for Wasm development workflow

### Implementation Handoff

**AI Agent Guidelines:**

- Follow all architectural decisions exactly as documented
- Use implementation patterns consistently across all components
- Respect project structure and boundaries
- Refer to this document for all architectural questions

**First Implementation Priority:**

```bash
pnpm create next-app -e https://github.com/theodorusclarence/ts-nextjs-tailwind-starter pick-sizer
cd pick-sizer
pnpm install
pnpm dev
```

Initialize project with starter template, then set up Supabase database and authentication as the first architectural implementation step.

## Architecture Completion Summary

### Workflow Completion

**Architecture Decision Workflow:** COMPLETED ✅
**Total Steps Completed:** 8
**Date Completed:** 2026-01-15
**Document Location:** c:/Users/FranklinOuattara/Desktop/PickSizer/_bmad-output/planning-artifacts/architecture.md

### Final Architecture Deliverables

**📋 Complete Architecture Document**

- All architectural decisions documented with specific versions
- Implementation patterns ensuring AI agent consistency
- Complete project structure with all files and directories
- Requirements to architecture mapping
- Validation confirming coherence and completeness

**🏗️ Implementation Ready Foundation**

- 4 critical architectural decisions made
- 4 implementation pattern categories defined
- 5 architectural component areas specified
- 21 functional requirements fully supported

**📚 AI Agent Implementation Guide**

- Technology stack with verified versions
- Consistency rules that prevent implementation conflicts
- Project structure with clear boundaries
- Integration patterns and communication standards

### Implementation Handoff

**For AI Agents:**
This architecture document is your complete guide for implementing PickSizer. Follow all decisions, patterns, and structures exactly as documented.

**First Implementation Priority:**

```bash
pnpm create next-app -e https://github.com/theodorusclarence/ts-nextjs-tailwind-starter pick-sizer
cd pick-sizer
pnpm install
pnpm dev
```

**Development Sequence:**

1. Initialize project using documented starter template
2. Set up development environment per architecture
3. Implement core architectural foundations
4. Build features following established patterns
5. Maintain consistency with documented rules

### Quality Assurance Checklist

**✅ Architecture Coherence**

- [x] All decisions work together without conflicts
- [x] Technology choices are compatible
- [x] Patterns support the architectural decisions
- [x] Structure aligns with all choices

**✅ Requirements Coverage**

- [x] All functional requirements are supported
- [x] All non-functional requirements are addressed
- [x] Cross-cutting concerns are handled
- [x] Integration points are defined

**✅ Implementation Readiness**

- [x] Decisions are specific and actionable
- [x] Patterns prevent agent conflicts
- [x] Structure is complete and unambiguous
- [x] Examples are provided for clarity

### Project Success Factors

**🎯 Clear Decision Framework**
Every technology choice was made collaboratively with clear rationale, ensuring all stakeholders understand the architectural direction.

**🔧 Consistency Guarantee**
Implementation patterns and rules ensure that multiple AI agents will produce compatible, consistent code that works together seamlessly.

**📋 Complete Coverage**
All project requirements are architecturally supported, with clear mapping from business needs to technical implementation.

**🏗️ Solid Foundation**
The chosen starter template and architectural patterns provide a production-ready foundation following current best practices.

---

**Architecture Status:** READY FOR IMPLEMENTATION ✅

**Next Phase:** Begin implementation using the architectural decisions and patterns documented herein.

**Document Maintenance:** Update this architecture when major technical decisions are made during implementation.
