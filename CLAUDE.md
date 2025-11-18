# CLAUDE.md - Developer Guide for Ironmount

This document provides comprehensive information for developers using Claude Code to work on Ironmount, as well as context to help Claude understand the codebase.

## Project Overview

**Ironmount** is a backup automation tool that helps users save data across multiple storage backends. Built on top of Restic, it provides a modern web interface to schedule, manage, and monitor encrypted backups.

### Key Features
- Automated backups with encryption, compression, and retention policies (powered by Restic)
- Flexible scheduling for automated backup jobs
- End-to-end encryption
- Multi-protocol support: NFS, SMB, WebDAV, local directories
- Multiple repository backends: Local, S3, Google Cloud Storage, Azure Blob Storage, rclone
- Docker volume plugin support
- Web-based management interface

## Architecture Overview

Ironmount is a full-stack TypeScript application with the following architecture:

```
┌─────────────────────────────────────────────────┐
│              React Router (v7)                  │
│         (SSR + Client-side routing)             │
├─────────────────────────────────────────────────┤
│                 Hono Server                     │
│           (API + SSR rendering)                 │
├─────────────────────────────────────────────────┤
│              Business Logic                     │
│  (Volumes, Repositories, Backups, Auth)         │
├─────────────────────────────────────────────────┤
│         Drizzle ORM + SQLite                    │
├─────────────────────────────────────────────────┤
│      External Tools & Integrations              │
│  (Restic, rclone, Docker volume plugin)         │
└─────────────────────────────────────────────────┘
```

### Core Components

1. **Frontend (React Router v7)**
   - Server-side rendering (SSR)
   - Client-side routing and hydration
   - TanStack Query for data fetching
   - Tailwind CSS for styling
   - Radix UI for accessible components

2. **Backend (Hono + Bun)**
   - RESTful API built with Hono
   - OpenAPI documentation with Scalar
   - JWT-based authentication
   - Event streaming (Server-Sent Events)
   - Docker volume plugin server (Unix socket)

3. **Database (SQLite + Drizzle ORM)**
   - SQLite for data persistence
   - Drizzle for type-safe database queries
   - Database migrations in `app/drizzle/`

4. **External Tools**
   - **Restic**: Core backup engine
   - **rclone**: Cloud storage connectivity
   - **FUSE**: Volume mounting

## Tech Stack

### Runtime & Build Tools
- **Bun**: JavaScript runtime and package manager (v1.3.1)
- **Vite**: Build tool and dev server
- **TypeScript**: Type safety (v5.9.3)

### Frontend
- **React Router v7**: Framework with SSR
- **React 19**: UI library
- **TanStack Query**: Server state management
- **Tailwind CSS v4**: Styling
- **Radix UI**: Accessible component primitives
- **React Hook Form**: Form management
- **Recharts**: Data visualization
- **Lucide React**: Icons
- **Sonner**: Toast notifications

### Backend
- **Hono**: Web framework (v4.10.5)
- **Drizzle ORM**: Database toolkit (v0.44.7)
- **SQLite**: Database
- **hono-openapi**: OpenAPI integration
- **@scalar/hono-api-reference**: API documentation
- **winston**: Logging
- **node-cron**: Job scheduling
- **dockerode**: Docker API client

### Validation & Schema
- **ArkType**: Runtime type validation
- **Zod/ArkType**: Schema validation

### Code Quality
- **Biome**: Linter and formatter
- **TypeScript**: Type checking

## Project Structure

```
ironmount/
├── app/                          # Application source code
│   ├── client/                   # Frontend code
│   │   ├── api-client/          # Auto-generated API client
│   │   ├── components/          # React components
│   │   ├── hooks/               # Custom React hooks
│   │   ├── lib/                 # Client utilities
│   │   ├── modules/             # Feature modules (UI)
│   │   └── routes/              # Route components
│   │
│   ├── server/                   # Backend code
│   │   ├── core/                # Core functionality
│   │   │   ├── capabilities.ts  # System capability detection
│   │   │   ├── config.ts        # Configuration management
│   │   │   ├── constants.ts     # Application constants
│   │   │   ├── events.ts        # Event emitter
│   │   │   └── scheduler.ts     # Job scheduler
│   │   │
│   │   ├── db/                  # Database
│   │   │   ├── db.ts           # Database client
│   │   │   └── schema.ts       # Database schema
│   │   │
│   │   ├── jobs/                # Background jobs
│   │   │   ├── backup-execution.ts
│   │   │   ├── cleanup-dangling.ts
│   │   │   ├── cleanup-sessions.ts
│   │   │   ├── healthchecks.ts
│   │   │   └── repository-healthchecks.ts
│   │   │
│   │   ├── modules/             # Feature modules
│   │   │   ├── auth/           # Authentication
│   │   │   ├── backends/       # Volume backends (NFS, SMB, WebDAV, Directory)
│   │   │   ├── backups/        # Backup management
│   │   │   ├── driver/         # Docker volume plugin
│   │   │   ├── events/         # Server-sent events
│   │   │   ├── lifecycle/      # Startup/shutdown
│   │   │   ├── repositories/   # Repository management
│   │   │   ├── system/         # System information
│   │   │   └── volumes/        # Volume management
│   │   │
│   │   ├── utils/               # Server utilities
│   │   │   ├── crypto.ts       # Encryption utilities
│   │   │   ├── errors.ts       # Error handling
│   │   │   ├── logger.ts       # Logging
│   │   │   ├── mountinfo.ts    # Mount information
│   │   │   ├── rclone.ts       # rclone integration
│   │   │   ├── restic.ts       # Restic integration
│   │   │   ├── sanitize.ts     # Input sanitization
│   │   │   ├── spawn.ts        # Process spawning
│   │   │   └── timeout.ts      # Timeout utilities
│   │   │
│   │   └── index.ts             # Server entry point
│   │
│   ├── drizzle/                 # Database migrations
│   ├── middleware/              # Middleware
│   ├── schemas/                 # Shared schemas
│   ├── utils/                   # Shared utilities
│   ├── app.css                  # Global styles
│   ├── context.ts               # React context
│   ├── root.tsx                 # Root component
│   └── routes.ts                # Route configuration
│
├── assets/                      # Static assets
├── public/                      # Public files
├── screenshots/                 # Documentation screenshots
├── LICENSES/                    # Third-party licenses
│
├── biome.json                   # Biome configuration
├── Dockerfile                   # Docker configuration
├── docker-compose.yml           # Docker Compose
├── drizzle.config.ts           # Drizzle configuration
├── react-router.config.ts      # React Router configuration
├── tsconfig.json               # TypeScript configuration
├── package.json                # Dependencies and scripts
└── README.md                   # User documentation
```

## Development Setup

### Prerequisites
- **Bun** v1.3.1 or later
- **Docker** and **Docker Compose** (for containerized development)

### Local Development

1. **Install dependencies:**
   ```bash
   bun install
   ```

2. **Start development server:**
   ```bash
   bun run dev
   ```
   The application will be available at `http://localhost:4096`

3. **Type checking:**
   ```bash
   bun run tsc
   ```

4. **Linting:**
   ```bash
   bun run lint
   ```

5. **Generate database migrations:**
   ```bash
   bun run gen:migrations
   ```

6. **View database:**
   ```bash
   bun run studio
   ```

### Docker Development

Development with Docker (includes all dependencies like Restic, rclone):
```bash
bun run start:dev
```

Production build with Docker:
```bash
bun run start:prod
```

### Building

```bash
bun run build
```

The build output goes to the `dist/` directory:
- `dist/client/`: Frontend assets
- `dist/server/`: Server bundle

## Key Concepts & Domain Knowledge

### Core Domain Entities

1. **Volumes**
   - Represent data sources to be backed up
   - Types: NFS, SMB, WebDAV, Local Directory
   - Each volume is mounted using FUSE
   - Located in `/var/lib/ironmount/mounts/<volume-name>`

2. **Repositories**
   - Storage locations for encrypted backups
   - Types: Local, S3, Google Cloud Storage, Azure, rclone
   - Managed by Restic
   - Local repos stored at `/var/lib/ironmount/repositories/<repo-name>`

3. **Backup Jobs**
   - Define automated backup schedules
   - Link a volume to a repository
   - Include retention policies (keep daily, weekly, monthly, yearly)
   - Scheduled using cron expressions

4. **Snapshots**
   - Point-in-time backups created by Restic
   - Immutable and deduplicated
   - Can be browsed and restored

### Backend Module Pattern

Each feature module follows a consistent pattern:

```
module/
├── module.controller.ts    # API routes and OpenAPI spec
├── module.service.ts       # Business logic
└── module.dto.ts           # Data transfer objects / validation schemas
```

**Example flow:**
1. Controller receives HTTP request
2. Validates input using DTO schemas (ArkType)
3. Calls service method
4. Service performs business logic
5. Returns response or throws error
6. Error middleware handles errors uniformly

### Volume Backends

Each backend implements mounting/unmounting logic:
- **Directory**: Direct filesystem access
- **NFS**: Network File System mounting
- **SMB**: Samba/CIFS mounting
- **WebDAV**: WebDAV over FUSE (using davfs2)

All backends extend from `app/server/modules/backends/backend.ts`

### Restic Integration

Restic commands are spawned as child processes:
- Initialization: `restic init`
- Backup: `restic backup`
- List snapshots: `restic snapshots`
- Restore: `restic restore`
- Check: `restic check`

See `app/server/utils/restic.ts` for Restic utilities.

### Authentication

- JWT-based authentication
- Initial setup creates default password
- Auth middleware validates tokens
- See `app/server/modules/auth/`

### Job Scheduling

Background jobs use `node-cron`:
- Backup execution
- Repository health checks
- Volume health checks
- Cleanup tasks (dangling mounts, old sessions)

See `app/server/core/scheduler.ts` and `app/server/jobs/`

### Events System

Server-sent events (SSE) for real-time updates:
- Backup progress
- Job status changes
- System events

See `app/server/core/events.ts` and `app/server/modules/events/`

### Docker Volume Plugin

Ironmount can act as a Docker volume plugin:
- Exposes Unix socket at `/run/docker/plugins/ironmount.sock`
- Implements Docker Volume Plugin API v2
- Allows mounting Ironmount volumes in other containers

See `app/server/modules/driver/`

## Common Development Tasks

### Adding a New API Endpoint

1. Create or update the controller:
   ```typescript
   // app/server/modules/mymodule/mymodule.controller.ts
   import { Hono } from "hono";
   import { openAPI } from "hono-openapi";

   export const myModuleController = new Hono()
     .post("/create", openAPI({
       tags: ["MyModule"],
       description: "Create something",
       request: {
         body: {
           content: {
             "application/json": {
               schema: createMyThingDto,
             },
           },
         },
       },
       responses: {
         200: {
           description: "Success",
           content: {
             "application/json": {
               schema: myThingResponseDto,
             },
           },
         },
       },
     }), async (c) => {
       const data = await c.req.json();
       // Call service method
       const result = await myModuleService.create(data);
       return c.json(result);
     });
   ```

2. Register in `app/server/index.ts`:
   ```typescript
   app.route("/api/v1/mymodule", myModuleController.use(requireAuth))
   ```

3. Regenerate API client:
   ```bash
   bun run gen:api-client
   ```

### Adding a Database Table

1. Update schema in `app/server/db/schema.ts`:
   ```typescript
   export const myTable = sqliteTable("my_table", {
     id: text("id").primaryKey(),
     name: text("name").notNull(),
     createdAt: integer("created_at", { mode: "timestamp" }).notNull(),
   });
   ```

2. Generate migration:
   ```bash
   bun run gen:migrations
   ```

3. Migration files created in `app/drizzle/`

4. Migrations run automatically on server start

### Adding a New Volume Backend

1. Create backend class in `app/server/modules/backends/mybackend/`:
   ```typescript
   import { Backend } from "../backend";

   export class MyBackend extends Backend {
     async mount(): Promise<void> {
       // Implement mounting logic
     }

     async unmount(): Promise<void> {
       // Implement unmounting logic
     }
   }
   ```

2. Register in volume service
3. Add UI components in `app/client/modules/volumes/`

### Working with Restic

Use the utilities in `app/server/utils/restic.ts`:
```typescript
import { spawnRestic } from "~/server/utils/restic";

const result = await spawnRestic(
  ["snapshots", "--json"],
  { repo: "/path/to/repo", password: "secret" }
);
```

### Adding a Background Job

1. Create job file in `app/server/jobs/`:
   ```typescript
   import { schedule } from "~/server/core/scheduler";

   export function startMyJob() {
     schedule("my-job", "0 * * * *", async () => {
       // Job logic
     });
   }
   ```

2. Call from `app/server/modules/lifecycle/startup.ts`

## Code Style & Conventions

### Code Formatting
- **Formatter**: Biome
- **Indent**: Tabs
- **Line width**: 120 characters
- **Quotes**: Double quotes for JavaScript/TypeScript
- **Format on save**: Recommended

Run formatter:
```bash
bun run lint
```

### TypeScript
- **Strict mode**: Enabled
- **Path aliases**: Use `~/` for `app/` directory
  ```typescript
  import { db } from "~/server/db/db";
  ```

### Naming Conventions
- **Files**: kebab-case (e.g., `volume-controller.ts`)
- **Components**: PascalCase (e.g., `VolumeList.tsx`)
- **Variables/Functions**: camelCase
- **Constants**: UPPER_SNAKE_CASE
- **Types/Interfaces**: PascalCase

### Error Handling
- Use `http-errors-enhanced` for HTTP errors
- Service methods throw errors, controllers catch them
- Global error handler in `app/server/index.ts`
- Error utilities in `app/server/utils/errors.ts`

### Logging
- Use Winston logger from `app/server/utils/logger.ts`
- Log levels: error, warn, info, debug
- Example:
  ```typescript
  import { logger } from "~/server/utils/logger";
  logger.info("Backup started", { backupId });
  logger.error("Backup failed", { error });
  ```

### Database Queries
- Use Drizzle ORM type-safe queries
- Import schema: `import { volumes } from "~/server/db/schema"`
- Example:
  ```typescript
  import { db } from "~/server/db/db";
  import { volumes } from "~/server/db/schema";
  import { eq } from "drizzle-orm";

  const volume = await db.select()
    .from(volumes)
    .where(eq(volumes.id, volumeId))
    .get();
  ```

## Testing & Quality

### Type Checking
```bash
bun run tsc
```

### Linting
```bash
bun run lint        # Check for issues
bun run lint --fix  # Auto-fix issues
```

### CI Linting
```bash
bun run lint:ci
```

## Important Files & Locations

### Configuration Files
- `tsconfig.json`: TypeScript configuration
- `biome.json`: Linter/formatter configuration
- `react-router.config.ts`: React Router configuration
- `drizzle.config.ts`: Database configuration
- `.env`: Environment variables (gitignored)

### Entry Points
- `app/server/index.ts`: Server entry point
- `app/root.tsx`: React root component
- `app/routes.ts`: Route configuration

### Key Utilities
- `app/server/utils/logger.ts`: Logging
- `app/server/utils/spawn.ts`: Process spawning
- `app/server/utils/restic.ts`: Restic integration
- `app/server/utils/rclone.ts`: rclone integration
- `app/server/utils/errors.ts`: Error handling
- `app/server/utils/crypto.ts`: Encryption

### Database
- Schema: `app/server/db/schema.ts`
- Client: `app/server/db/db.ts`
- Migrations: `app/drizzle/`
- Database file: `./data/ironmount.db` (development)

## Environment Variables

Common environment variables:
- `NODE_ENV`: Environment (development/production)
- `VITE_APP_VERSION`: Application version (injected at build time)
- Database location configured in `drizzle.config.ts`

## Tips for Working with Claude Code

### Understanding the Codebase

1. **Start with domain models**: Review `app/server/db/schema.ts` to understand data structures
2. **Follow the module pattern**: Each feature has controller → service → database flow
3. **Check the API**: Browse `/api/v1/docs` for OpenAPI documentation
4. **Trace request flow**: Request → Controller → Service → Database/External Tool

### Making Changes

1. **Always run type checking** after changes: `bun run tsc`
2. **Format code** before committing: `bun run lint`
3. **Test in Docker** if working with volumes/mounting: `bun run start:dev`
4. **Regenerate API client** after API changes: `bun run gen:api-client`
5. **Create migrations** after schema changes: `bun run gen:migrations`

### Common Pitfalls

1. **Volume mounting requires privileged access**: Docker needs `SYS_ADMIN` capability and `/dev/fuse`
2. **Restic operations are async**: Always handle subprocess errors
3. **Database is SQLite**: No concurrent writes, use transactions carefully
4. **SSR context**: Some code runs server-side, some client-side (check `.server` and `.client` directories)
5. **Path aliases**: Remember to use `~/` for imports from `app/`

### Debugging

1. **Server logs**: Check console output (Winston logging)
2. **Database inspection**: Use `bun run studio` for Drizzle Studio
3. **API testing**: Use `/api/v1/docs` for interactive API testing
4. **Network requests**: Check browser DevTools for client-side API calls
5. **Docker logs**: `docker compose logs -f` for containerized debugging

### Performance Considerations

1. **Restic operations can be slow**: Use proper loading states in UI
2. **Large backups**: Handle streaming and progress updates
3. **Mount operations**: FUSE mounts can be resource-intensive
4. **Database queries**: Use indexes and limit results appropriately

## External Dependencies

### System Tools
- **Restic v0.18.1**: Backup engine (bundled in Docker image)
- **rclone**: Cloud storage (bundled in Docker image)
- **davfs2**: WebDAV mounting (bundled in Docker image)
- **FUSE**: Filesystem in userspace

### Where They're Used
- Restic: `app/server/utils/restic.ts`, backup jobs
- rclone: `app/server/utils/rclone.ts`, repository backends
- FUSE: Volume mounting in backends
- Docker: Optional volume plugin feature

## Contributing Guidelines

1. **Code quality**: Ensure lint passes before committing
2. **Type safety**: Fix all TypeScript errors
3. **Documentation**: Update this file if adding major features
4. **Commit messages**: Use clear, descriptive messages
5. **Testing**: Manually test changes in Docker environment

## Additional Resources

- [Restic Documentation](https://restic.readthedocs.io/)
- [rclone Documentation](https://rclone.org/)
- [React Router v7 Docs](https://reactrouter.com/)
- [Hono Documentation](https://hono.dev/)
- [Drizzle ORM Documentation](https://orm.drizzle.team/)
- [Biome Documentation](https://biomejs.dev/)

## Getting Help

- **Issues**: Check GitHub issues for known problems
- **Logs**: Check server logs for error details
- **Database**: Use Drizzle Studio to inspect data
- **API**: Use Scalar docs at `/api/v1/docs` to test endpoints

---

**Last Updated**: 2025-11-18
**Project Version**: 0.x.x (pre-1.0, expect breaking changes)
