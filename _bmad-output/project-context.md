---
project_name: 'PickSizer'
user_name: 'FranklinOuattara'
date: '2026-01-15'
sections_completed: ['technology_stack', 'language_rules', 'framework_rules', 'testing_rules', 'code_quality_rules', 'workflow_rules']
status: 'complete'
rule_count: 25
optimized_for_llm: true
existing_patterns_found: { 7 }
---

# Project Context for AI Agents

_This file contains critical rules and patterns that AI agents must follow when implementing code in this project. Focus on unobvious details that agents might otherwise miss._

---

## Technology Stack & Versions

- **Frontend**: Next.js 15.4.3 with App Router, React 19.1.0, TypeScript 5.8.3
- **Database**: Supabase (PostgreSQL) with real-time features and Row-Level Security
- **State Management**: XState for aviation-grade safety and authority management
- **Image Processing**: Rust WebAssembly with Wasm-Pack for <300ms performance
- **Styling**: Shadcn UI + Tailwind CSS 4.1.11 with CSS Variables for theming
- **Testing**: Jest 27.5.1 with Testing Library
- **Build Tools**: pnpm, ESLint, Prettier, Husky for code quality

## Critical Implementation Rules

### Language-Specific Rules

- **TypeScript**: Strict mode required, path aliases `@/*` for imports
- **Naming**: Components PascalCase, functions camelCase, database snake_case
- **Error Handling**: XState machines for state errors, Supabase RLS violations as security errors
- **WebAssembly**: Memory safety checks, Rust compilation with Wasm-Pack

### Framework-Specific Rules

- **React**: Custom hooks in `/src/hooks/`, shared components in `/src/components/ui/`
- **Shadcn UI**: Use components from `/src/components/ui/`, customize via CSS Variables
- **XState**: SCREAMING_SNAKE_CASE events, Observer → Autopilot authority transitions
- **Next.js**: App Router with route groups, API routes in `/src/app/api/`
- **Performance**: WebAssembly for <300ms processing, skeleton UI states

### Testing Rules

- **Organization**: Co-located tests `*.test.tsx`, feature tests in `/tests/features/`
- **Mocks**: Supabase and WebAssembly mocks in `/tests/__mocks__/`
- **Coverage**: Focus on image processing, state management, safety systems
- **Boundaries**: Unit for components/hooks, Integration for APIs/Wasm, E2E for user journeys

### Code Quality & Style Rules

- **Linting**: ESLint + Prettier with Husky pre-commit hooks
- **Naming**: Components PascalCase, functions camelCase, database snake_case
- **Events**: SCREAMING_SNAKE_CASE for XState events
- **Documentation**: JSDoc for complex functions, Storybook for UI components

### Development Workflow Rules

- **Commits**: Conventional commits enforced by Husky
- **Branches**: Feature branches with descriptive names
- **PRs**: Automated checks via GitHub Actions
- **Deployment**: Vercel automatic deployment from main branch

---

**Project Context Status:** COMPLETE 

**AI Agent Guidelines:**
Read this file before implementing any code. Follow all rules exactly to ensure consistent, high-quality implementation that aligns with the architectural decisions documented in the architecture.md file.

---

## Usage Guidelines

**For AI Agents:**

- Read this file before implementing any code
- Follow ALL rules exactly as documented
- When in doubt, prefer the more restrictive option
- Update this file if new patterns emerge

**For Humans:**

- Keep this file lean and focused on agent needs
- Update when technology stack changes
- Review quarterly for outdated rules
- Remove rules that become obvious over time

Last Updated: 2026-01-15
