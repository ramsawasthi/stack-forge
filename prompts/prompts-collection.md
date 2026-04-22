# Quarks Technosoft — Cursor AI Prompt Library
## MERN · Spring Boot · Full-Stack Development

> Copy any prompt directly into Cursor. Prompts marked `[Chat]` use **Cmd+L**. Prompts marked `[Composer]` use **Cmd+I**. Prompts marked `[Inline]` use **Cmd+K** on selected code.

---

## TABLE OF CONTENTS

1. [Project Setup](#1-project-setup)
2. [Requirements & PRD](#2-requirements--prd)
3. [Schema & Data Model](#3-schema--data-model)
4. [Backend — MERN (Express + Node)](#4-backend--mern-express--node)
5. [Backend — Spring Boot](#5-backend--spring-boot)
6. [Frontend — React + TypeScript](#6-frontend--react--typescript)
7. [Testing](#7-testing)
8. [Code Review & Refactoring](#8-code-review--refactoring)
9. [Debugging](#9-debugging)
10. [DevOps & Docker](#10-devops--docker)
11. [Documentation](#11-documentation)

---

## 1. PROJECT SETUP

### 1.1 Scaffold MERN monorepo `[Composer]`
```
Create a MERN monorepo using pnpm workspaces following .cursorrules.

Generate:
- pnpm-workspace.yaml listing apps/api and apps/web
- apps/api/: Express + TypeScript, tsconfig, package.json with all deps
- apps/web/: Vite + React 19 + TypeScript, tailwind.config.ts
- packages/shared/: shared TypeScript types and zod schemas
- docker-compose.yml: mongodb, redis, api, web services with healthchecks
- .env.example with all required variables and inline comments
- Root package.json with workspace scripts: dev, build, test, lint

Follow .cursorrules for all standards. No placeholders.
```

### 1.2 Scaffold Spring Boot project `[Composer]`
```
Create a Spring Boot 3.3 project following .cursorrules.

Generate:
- pom.xml with: spring-boot-starter-web, data-jpa, security, validation,
  actuator, cache, data-redis, lombok, mapstruct, flyway, postgresql driver,
  springdoc-openapi, testcontainers, mockito
- src/main/resources/application.yml (no secrets — use ${ENV_VAR})
- src/main/resources/application-dev.yml
- com.quarks.opportunitymapper.core.config.AppConfig (@ConfigurationProperties)
- com.quarks.opportunitymapper.core.exception.AppException hierarchy (see .cursorrules)
- com.quarks.opportunitymapper.api.exception.GlobalExceptionHandler
- com.quarks.opportunitymapper.api.dto.ApiResponse record
- Dockerfile: multi-stage maven build + JRE alpine runtime
- docker-compose.yml: app, postgres:16, redis:7 with healthchecks

No placeholders. Full path comment on line 1 of every file.
```

### 1.3 Create .cursorrules for new project `[Chat]`
```
I am starting a new project with this stack: [describe your stack].

Generate a .cursorrules file that:
1. Defines the project structure (folder names and what goes where)
2. Sets architecture rules (layer order, what each layer can/cannot do)
3. Sets language-specific standards (TypeScript strict, Java constructor injection, etc.)
4. Sets testing rules (unit vs integration, mocking strategy)
5. Sets Docker and environment rules
6. Sets file naming and export conventions

Output only the .cursorrules file content. No explanation.
```

---

## 2. REQUIREMENTS & PRD

### 2.1 Generate PRD from one-liner `[Chat]`
```
You are a senior product manager.

Generate a complete PRD for: [describe your feature/product in 1-2 sentences]

Include:
1. Problem statement — why this needs to exist
2. Target users — 2-3 personas with name, role, primary goal
3. Functional requirements — minimum 8, "The system shall..." format
4. Non-functional requirements — performance, security, reliability
5. User stories — minimum 6, with 3 acceptance criteria each
6. Out of scope — what v1 explicitly does NOT do
7. Open questions — 3-5 decisions needed before development

Format: clean markdown with numbered sections.
```

### 2.2 Stress-test requirements for gaps `[Chat]`
```
Review the PRD above as a senior software architect.

Find gaps that would cause problems during development or QA:
1. Missing edge cases not covered by user stories
2. Ambiguous requirements two developers would implement differently
3. Missing rate limiting / retry / timeout handling
4. Security gaps (input sanitisation, auth, data exposure)
5. Missing error states (what happens when X is unavailable?)
6. Concurrency issues (what if two users do Y simultaneously?)
7. Data consistency issues (what if the operation is interrupted?)

For each gap: state the problem, production risk, and the fix.
Output as a numbered list.
```

### 2.3 Convert user stories to tasks `[Chat]`
```
Convert the user stories from @REQUIREMENTS.md into development tasks.

For each user story, create:
- Task title
- Layer(s) affected: [DB schema / API / service / frontend]
- Estimated complexity: [S=half day / M=1 day / L=2+ days]
- Dependencies: which tasks must complete before this one
- Acceptance test: one testable statement proving the task is done

Output as a markdown table.
```

---

## 3. SCHEMA & DATA MODEL

### 3.1 MongoDB schema design `[Composer]`
```
Using @REQUIREMENTS.md, design the MongoDB schema.

Rules:
- All schemas: timestamps: true (createdAt, updatedAt auto-managed)
- All schemas: deletedAt: Date (null = active, soft delete pattern)
- All schemas: _id as ObjectId (default MongoDB)
- Index every field used in .find() — declare in schema
- Use references (ObjectId ref) not embedding for large related documents
- Embed only when data is always fetched together and changes together

Collections to create:
[List your collections here]

For each schema output:
1. Mongoose schema definition with full type annotations
2. TypeScript interface
3. Zod validation schema (for API input validation)
4. Required indexes with justification comment
5. Seed data if applicable

File per collection. Full path on line 1. No placeholders.
```

### 3.2 PostgreSQL schema design `[Composer]`
```
Using @REQUIREMENTS.md, design the PostgreSQL schema.

Rules:
- UUID primary keys (gen_random_uuid())
- All tables: created_at, updated_at TIMESTAMPTZ (trigger-managed)
- All tables: deleted_at TIMESTAMPTZ NULL (soft delete)
- Explicit ON DELETE behaviour on all foreign keys
- Indexes on all FK columns and query filter columns
- JSONB for flexible/variable data

Tables to create: [list tables]

Output:
1. CREATE TABLE statements with all constraints
2. CREATE INDEX statements with rationale comments
3. updated_at trigger function applied to all tables
4. Alembic (Python) or Flyway (Java) migration file
5. Seed data inserts where applicable
```

### 3.3 Review schema for N+1 problems `[Chat]`
```
Review @src/models/ (or @src/main/java/.../model/) for N+1 query risks.

For each relationship:
1. Identify the fetch strategy (lazy/eager or embed/reference)
2. Show the query generated for the most common read pattern
3. Flag any pattern that would result in N+1 queries
4. Write the optimised query (JOIN FETCH, $lookup, or populate with select)

Output: list of relationships, risk level, and the correct query.
```

---

## 4. BACKEND — MERN (Express + Node)

### 4.1 Create a complete API resource `[Composer]`
```
Using @REQUIREMENTS.md @src/models/ @src/core/

Create a complete API resource for: [resource name]

Files to generate:
- src/models/[Resource].model.ts — Mongoose schema + TS interface + Zod schema
- src/repositories/[resource].repository.ts — all DB operations, .lean() on reads
- src/services/[resource].service.ts — business logic, calls repository
- src/routes/[resource].routes.ts — Express router, input validation, calls service
- src/api/dto/[resource].dto.ts — request/response DTOs

Endpoints:
- POST /api/v1/[resources] — create
- GET /api/v1/[resources] — list with pagination (?page&limit&sort)
- GET /api/v1/[resources]/:id — get by ID
- PATCH /api/v1/[resources]/:id — partial update
- DELETE /api/v1/[resources]/:id — soft delete

Each endpoint:
- Validates input through Zod schema
- Returns { success: true, data: T } or { success: false, errorCode, message }
- Throws typed AppError subclasses (not generic Error)

Follow .cursorrules. No placeholders.
```

### 4.2 Add authentication middleware `[Composer]`
```
Using @src/core/config.ts @src/models/

Implement JWT authentication:

- src/middleware/auth.middleware.ts
  authenticate(req, res, next): verify JWT from Authorization header
  If valid: attach decoded user to req.user, call next()
  If invalid/expired: throw UnauthorizedError

- src/middleware/authorize.middleware.ts
  authorize(...roles: string[]): check req.user.role against allowed roles

- src/core/auth.service.ts
  generateTokens(userId): returns { accessToken (15min), refreshToken (7d) }
  verifyAccessToken(token): returns payload or throws
  refreshTokens(refreshToken): rotate refresh token

Follow .cursorrules. No placeholder logic.
```

### 4.3 Add external API integration `[Composer]`
```
Using @src/core/config.ts @src/core/exceptions.ts

Create an external API client for: [API name — e.g. Claude, Stripe, Twilio]

File: src/infrastructure/external/[service].client.ts

Requirements:
- httpx/axios with timeout: 10000ms
- Retry: 3 attempts, exponential backoff (1s, 2s, 4s) on 429 and 503 only
- Rate limit tracking: track requests per minute, throw RateLimitError if exceeded
- All responses validated through Zod schema
- Structured logging on every request: service, method, duration, status
- On failure: throw ExternalServiceError with service name and status code

Follow .cursorrules.
```

### 4.4 Create Express error handler `[Composer]`
```
Using @src/core/exceptions.ts

Create src/middleware/errorHandler.ts — central error handling middleware.

Handle:
- AppError subclasses → return { success: false, errorCode, message } with correct status
- ZodError (validation) → 422 with field-level errors array
- MongoError code 11000 (duplicate key) → 409 ConflictError with field name
- JsonWebTokenError → 401 UnauthorizedError
- Unhandled Error → log full stack, return 500 with generic message (never expose internals)

All responses include: requestId (from req.headers['x-request-id'])
All errors logged with: userId, requestId, path, method, statusCode, stack (for 5xx)

No placeholders.
```

---

## 5. BACKEND — SPRING BOOT

### 5.1 Create a complete REST resource `[Composer]`
```
Using @REQUIREMENTS.md and following .cursorrules,
create a complete REST resource for: [Resource name]

Files to generate:
- domain/model/[Resource].java — JPA entity with UUID PK, audit fields, soft delete
- domain/repository/[Resource]Repository.java — JpaRepository + custom queries
- domain/service/[Resource]Service.java — interface
- domain/service/impl/[Resource]ServiceImpl.java — @Service @Transactional implementation
- api/dto/[Resource]Request.java — record with @Valid annotations
- api/dto/[Resource]Response.java — record (what we return to client)
- api/mapper/[Resource]Mapper.java — MapStruct mapper interface
- api/controller/[Resource]Controller.java — @RestController, calls service only

Endpoints:
- POST /api/v1/[resources] → 201 Created
- GET /api/v1/[resources] → paginated list (Pageable)
- GET /api/v1/[resources]/{id} → 200 or 404
- PATCH /api/v1/[resources]/{id} → 200
- DELETE /api/v1/[resources]/{id} → 204 (soft delete)

Flyway migration: db/migration/V[N]__create_[resources]_table.sql

Follow .cursorrules. Constructor injection only. No placeholders.
```

### 5.2 Add Spring Security JWT `[Composer]`
```
Using @src/main/java/.../config/ and following .cursorrules,
implement JWT authentication.

- security/JwtTokenProvider.java
  generateToken(UserDetails): signs with RS256, includes userId, roles, expiry
  validateToken(String): returns Claims or throws InvalidTokenException
  extractUserId(String): returns UUID

- security/JwtAuthenticationFilter.java extends OncePerRequestFilter
  Reads Authorization: Bearer {token}
  Validates → sets UsernamePasswordAuthenticationToken in SecurityContext

- config/SecurityConfig.java @Configuration
  SecurityFilterChain: stateless session, permit /api/v1/auth/**, require auth elsewhere
  Add JwtAuthenticationFilter before UsernamePasswordAuthenticationFilter
  CORS from @Value("${app.cors.allowed-origins}")

- api/controller/AuthController.java
  POST /api/v1/auth/login → returns access + refresh token
  POST /api/v1/auth/refresh → rotates refresh token
  POST /api/v1/auth/logout → invalidates refresh token in Redis

Constructor injection only. No placeholders.
```

### 5.3 Add caching with Redis `[Composer]`
```
Using @src/main/java/.../config/ and following .cursorrules,
implement Redis caching.

- infrastructure/config/CacheConfig.java
  RedisCacheManager with per-cache TTL configuration:
    companies: 1 hour
    research-results: 24 hours
    user-sessions: 30 minutes
  Key format: {cacheName}::{key}
  Jackson serializer for values (not JDK serialization)

- Apply to service methods:
  @Cacheable(value="research-results", key="#companySlug")
  @CacheEvict(value="research-results", key="#companySlug") on update/delete

- infrastructure/cache/CacheHelper.java
  Manual cache operations for cases @Cacheable can't handle
  checkAndAcquireLock(key, ttl): SETNX for research pipeline concurrency control

No placeholders.
```

### 5.4 Add async processing `[Composer]`
```
Using @src/main/java/.../config/ and following .cursorrules,
implement async processing for research pipeline.

- infrastructure/config/AsyncConfig.java @Configuration @EnableAsync
  ThreadPoolTaskExecutor named "researchExecutor":
    corePoolSize: 4, maxPoolSize: 10, queueCapacity: 50
    threadNamePrefix: "research-"
    rejectedExecutionHandler: CallerRunsPolicy (don't drop work)

- domain/service/impl/ResearchServiceImpl.java
  @Async("researchExecutor") on runResearchAsync()
  Returns CompletableFuture<ResearchResult>

- Add endpoint: POST /api/v1/research/async
  Returns 202 Accepted with { jobId: UUID }
  Client polls GET /api/v1/research/jobs/{jobId} for status

No placeholders.
```

---

## 6. FRONTEND — REACT + TYPESCRIPT

### 6.1 Create a feature module `[Composer]`
```
Using @packages/shared/types @src/lib/api.ts @src/types/

Create a complete feature module for: [feature name]

Files:
- src/features/[feature]/[Feature]Page.tsx — route entry, sets page title
- src/features/[feature]/[Feature]Container.tsx — data fetching with TanStack Query
- src/features/[feature]/[Feature]View.tsx — presentation, no hooks except local UI state
- src/features/[feature]/hooks/use[Feature].ts — mutation/query hooks
- src/features/[feature]/components/[Feature]Form.tsx — RHF + Zod form
- src/features/[feature]/index.ts — barrel export

Requirements:
- All 4 async states: loading (skeleton), error (ErrorMessage + retry), empty, success
- Form validation via Zod schema matching backend DTO
- Optimistic updates on mutations where appropriate
- Error toast on mutation failure via sonner or react-hot-toast

Follow .cursorrules. Tailwind only. No inline styles. No any types.
```

### 6.2 Create a data table with pagination `[Composer]`
```
Using @src/types/api.ts @src/lib/api.ts @src/components/ui/

Create a paginated data table component for: [resource name]

- src/components/[Resource]Table.tsx
  Props: data, columns, pagination, onPageChange, isLoading
  Use shadcn/ui Table component
  Loading: skeleton rows (match column count)
  Empty: "No [resources] found" with optional CTA
  Pagination: Previous/Next + page info "Page X of Y (Z total)"

- src/hooks/use[Resource]Table.ts
  useQuery for fetching with page/pageSize/sort params
  URL sync: read/write search params for page and sort state

Responsive: hide non-essential columns on mobile (hidden sm:table-cell)
Follow .cursorrules.
```

### 6.3 Create an API client function `[Composer]`
```
Using @src/types/api.ts

Add to src/lib/api.ts:

Function: [functionName](params: [ParamsType]): Promise<[ReturnType]>

Requirements:
- POST/GET/PATCH/DELETE to: /api/v1/[endpoint]
- Throw APIError (with status + errorCode + message) on non-2xx
- Parse response through Zod schema for runtime safety
- Include credentials: 'include' for cookie-based auth if applicable

TypeScript:
- Add [ReturnType] interface to src/types/api.ts if not already present
- Zod schema in src/lib/schemas.ts

No any types. No non-null assertions.
```

### 6.4 Add form with validation `[Composer]`
```
Using @src/types/api.ts @src/components/ui/

Create src/features/[feature]/components/[Name]Form.tsx

Form fields: [list fields with types]

Requirements:
- React Hook Form with zodResolver
- Zod schema validates: [list rules — required, min length, email format, etc.]
- Each field: Label + Input + error message below
- Submit button: disabled while submitting, shows spinner
- On success: [reset form / navigate / show toast]
- On error: display server error message below submit button

Use shadcn/ui: Form, FormField, FormItem, FormLabel, FormControl, FormMessage
Tailwind only. Full TypeScript. No any.
```

---

## 7. TESTING

### 7.1 Generate unit tests for any function `[Composer]`
```
Using @[file-under-test] @tests/[setup-file-if-any]

Write tests/unit/[test-file].ts (or .spec.ts) for [functionName].

Cover:
- Happy path: expected input → expected output (assert shape + values)
- Null / undefined inputs: what happens, what should happen
- Boundary values: empty array, single item, max items
- Each error case the function can throw
- Any external call must be mocked — no real HTTP, DB, or filesystem

Use:
- describe/it blocks with descriptive names
- beforeEach for shared setup
- @jest.mock / vi.mock for dependencies
- expect.objectContaining for partial object matching

No real external calls. Full path comment line 1.
```

### 7.2 Generate API integration tests `[Composer]`
```
Using @[router or controller file] @tests/setup/

Write integration tests for [endpoint group].

Setup:
- Spin up real DB (mongodb-memory-server for MERN, TestContainers for Spring)
- Seed test data in beforeEach, clean in afterEach
- Mock only: external API calls (use msw or Mockito)
- Use real services and repositories

Test cases for each endpoint:
- 200/201 happy path — assert response shape and DB state
- 422 validation error — missing field, wrong type, out of range
- 404 not found — non-existent ID
- 401 unauthenticated — missing/invalid token
- 403 forbidden — authenticated but wrong role
- 409 conflict — duplicate where unique constraint applies

Assert both: response body AND database state after mutation.
```

### 7.3 Find test coverage gaps `[Chat]`
```
Review @tests/ as a QA lead before a production release.

Find:
1. Functions in @src/ (or @src/main/java/) with no corresponding test
2. Happy-path-only tests missing error case coverage
3. Missing concurrent request tests (race conditions)
4. Missing input boundary tests (empty string, max length, special chars)
5. Tests that test mocks instead of real behaviour
6. Any test with no assertions (passes vacuously)

For each gap: file, function, missing case, write the missing test.
```

### 7.4 Write frontend component tests `[Composer]`
```
Using @src/features/[feature]/ @tests/setup/

Write tests for [ComponentName].tsx using @testing-library/react + vitest.

Mock:
- All API calls via msw handlers in tests/mocks/handlers.ts
- React Router navigation (if used)

Test:
- Loading state: correct skeleton shown while query pending
- Error state: error message shown, retry button present and clickable
- Empty state: empty message shown when data is []
- Success state: data renders correctly, key fields visible
- User interactions: [form submit / button click / filter change]
- Accessibility: no aria violations (use jest-axe)

queryByRole and queryByText over queryByTestId.
```

---

## 8. CODE REVIEW & REFACTORING

### 8.1 SOLID review `[Chat]`
```
Review @[file] for SOLID violations.

For each principle:
S - Does this class/function have more than one reason to change?
O - Would adding new behaviour require editing this class?
L - Can any subclass/implementation be swapped without breaking callers?
I - Does any interface force implementors to define unused methods?
D - Does this class instantiate its own dependencies instead of receiving them?

For each violation:
1. Quote the specific lines
2. Name the principle violated
3. Explain the production risk
4. Show the refactored version

Be specific — generic advice is not useful.
```

### 8.2 Performance review `[Chat]`
```
Review @[file or folder] for performance issues.

Check:
1. N+1 query patterns (loop with DB call inside)
2. Missing database indexes (fields filtered/sorted without index)
3. Over-fetching (returning full document when only 2 fields needed)
4. Missing pagination on list endpoints
5. Synchronous operations that should be async
6. Missing caching on expensive repeated computations
7. Memory leaks (event listeners not removed, intervals not cleared)

For each issue: location, impact, fix.
```

### 8.3 Security review `[Chat]`
```
Review @[file or folder] for security vulnerabilities.

Check:
1. SQL/NoSQL injection: user input used in queries without sanitisation
2. Sensitive data exposure: passwords, tokens, PII in API responses or logs
3. Missing authentication on endpoints that need it
4. Missing authorisation (authenticated but wrong role can access)
5. Hardcoded secrets or API keys
6. CORS too permissive (allow: *)
7. Missing rate limiting on auth endpoints
8. JWT: algorithm confusion attacks, missing expiry check
9. Dependency vulnerabilities (run: npm audit / mvn dependency-check)

For each issue: severity (HIGH/MEDIUM/LOW), location, fix.
```

### 8.4 Refactor to clean architecture `[Chat]`
```
Review @[file] for architecture violations according to .cursorrules.

Flag:
1. Business logic in a controller/router function
2. DB query inside a service (should be in repository)
3. HTTP types (Request/Response) inside a service
4. Missing dependency injection (concrete class instantiated inside another)
5. Missing error handling on async operations
6. Console.log or System.out.println (should use structured logger)

For each violation:
- Quote the line(s)
- Explain which layer rule is broken
- Show the correctly refactored version with code moved to the right layer
```

---

## 9. DEBUGGING

### 9.1 Debug any error `[Chat]`
```
Error: [paste full stack trace]
File: @[relevant-file]
Context: [what you were trying to do, what input triggered this]

1. What is the root cause (not the symptom)?
2. Which specific line is broken and why?
3. Show the minimal fix
4. What test would have caught this?
5. Is there a deeper design issue that allowed this bug to exist?
```

### 9.2 Debug async/await issue `[Chat]`
```
I have an async bug in @[file].

Symptom: [describe — e.g. "function returns before DB write completes"]

Check:
1. Is there a missing await anywhere in the call chain?
2. Is Promise rejection being swallowed somewhere?
3. Is there a race condition between two concurrent async operations?
4. Is there a mix of callbacks and promises?

Show the fixed version with explanation of what was wrong.
```

### 9.3 Fix failing test `[Chat]`
```
Test failing:
File: @[test-file]
Test name: "[test name]"
Error: [paste test output]

1. Is the test wrong or is the implementation wrong?
2. If the test is wrong: show the corrected test
3. If the implementation is wrong: show the fix
4. If both need changing: show both and explain why

Do not just make the test pass — fix the actual problem.
```

---

## 10. DEVOPS & DOCKER

### 10.1 Generate docker-compose `[Composer]`
```
Using .cursorrules, generate docker-compose.yml for this project.

Services:
- [api/app service]: build from Dockerfile, depends_on db + cache (healthy)
  env_file: .env, healthcheck on /health or /actuator/health
- [database]: correct image:version, named volume for data
  healthcheck: pg_isready or mongosh ping
- [cache]: redis:7-alpine, healthcheck: redis-cli ping
- [optional extras]: pgadmin/mongo-express only in dev profile

Rules:
- All credentials from environment variables — no hardcoded values
- Volume names: [project]-postgres-data or [project]-mongo-data
- Network: single bridge network, service names as hostnames
- .env.example with all vars, inline comments explaining each

Output: docker-compose.yml + .env.example
```

### 10.2 Generate multi-stage Dockerfile `[Composer]`
```
Generate a production-ready Dockerfile for [MERN/Spring Boot] following .cursorrules.

MERN (Node.js):
- Stage 1 (deps): node:20-alpine, install pnpm, copy package files, pnpm install --frozen-lockfile
- Stage 2 (build): copy src, run pnpm build
- Stage 3 (runtime): node:20-alpine, copy dist + node_modules, USER node (non-root), CMD

Spring Boot:
- Stage 1 (build): maven:3.9-eclipse-temurin-21, mvn package -DskipTests
- Stage 2 (runtime): eclipse-temurin:21-jre-alpine
  COPY --from=build target/*.jar app.jar
  JVM flags: -XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0
  USER 1001 (non-root)

Both: EXPOSE correct port, HEALTHCHECK instruction, no secrets in image.
```

### 10.3 Generate GitHub Actions CI `[Composer]`
```
Generate .github/workflows/ci.yml for this project.

Triggers: push to main and develop, pull_request to main

Jobs:
1. lint: run ESLint/Checkstyle, fail if errors
2. test: run unit + integration tests
   For integration: spin up services via docker-compose or testcontainers
   Upload coverage report to Codecov
3. build: build Docker image, tag with commit SHA
4. security-scan: run npm audit / OWASP dependency check
   Fail on HIGH severity vulnerabilities

Requirements:
- Cache: node_modules or Maven ~/.m2 between runs
- Fail fast: don't run build if tests fail
- Post PR comment with test summary
```

---

## 11. DOCUMENTATION

### 11.1 Generate API documentation `[Chat]`
```
Using @[router or controller file],
generate OpenAPI/Swagger documentation for all endpoints.

For each endpoint:
- Summary (one line)
- Description (when to use, any caveats)
- Request body schema with field descriptions and examples
- Response schema for 200, 201, 400, 401, 403, 404, 422, 500
- Example request (curl command)
- Example response (realistic JSON)

Output as OpenAPI 3.0 YAML.
```

### 11.2 Generate README `[Composer]`
```
Using @package.json (or @pom.xml) @docker-compose.yml @.env.example,
generate a complete README.md for this project.

Sections:
## Overview — what this does, who it's for
## Tech stack — with version numbers
## Prerequisites — what to install before starting
## Quick start — clone → env setup → docker up → first request (copy-paste commands)
## Development setup — without Docker, step by step
## Environment variables — table of all vars with description and example value
## API reference — key endpoints with example curl commands
## Running tests — commands for unit, integration, coverage
## Project structure — annotated folder tree
## Deployment — production Docker build and run commands
## Contributing — branch naming, commit format, PR process

Be specific — no vague "configure as needed" instructions.
```

### 11.3 Generate ADR (Architecture Decision Record) `[Chat]`
```
Write an Architecture Decision Record for this decision:

Decision: [e.g. "Use MongoDB instead of PostgreSQL for company profiles"]

Include:
- Status: [Proposed / Accepted / Superseded]
- Context: what problem were we solving, what constraints existed
- Decision: what we decided and the exact reasoning
- Alternatives considered: at least 2, with why they were rejected
- Consequences: positive outcomes, negative tradeoffs, future risks
- Related decisions: other ADRs this connects to

Format: standard ADR markdown template.
```

---

## QUICK REFERENCE — SHORTCUT TABLE

| Goal | Mode | Prompt Pattern |
|------|------|---------------|
| Fix a broken function | Cmd+K | "Fix this. Error: [paste error]" |
| Understand an error | Cmd+L | "What is the root cause of: [paste stack trace]" |
| Add a new feature | Cmd+I | Use templates from sections 4, 5, 6 |
| Review before commit | Cmd+L | "Review @[file] for SOLID and .cursorrules violations" |
| Generate tests | Cmd+I | Use templates from section 7 |
| Debug async issue | Cmd+L | Use template 9.2 |
| Refactor a file | Cmd+K | "Refactor to follow .cursorrules. Keep same API surface." |
| Write commit message | Cmd+L | "Write a conventional commit for changes to @[file]" |
| Find security issues | Cmd+L | Use template 8.3 |
| Explain unfamiliar code | Cmd+L | "Explain @[file] line by line. Focus on non-obvious parts." |

---

*Quarks Technosoft · Internal Use Only · Keep updated as new patterns emerge*
