# Project Structure & Organization

## Root Structure

```
├── src/                    # Source code
│   ├── main/              # Electron main process
│   ├── preload/           # Preload scripts
│   └── renderer/          # React renderer process
├── packages/              # Workspace packages
├── scripts/               # Build and utility scripts
├── docs/                  # Documentation
├── resources/             # Static resources and data
└── tests/                 # Test files
```

## Main Process (`src/main/`)

- **index.ts**: Application entry point and lifecycle management
- **services/**: Core system services (WindowService, MCPService, etc.)
- **mcpServers/**: Model Context Protocol server implementations
- **knowledge/**: Knowledge base and document processing
- **utils/**: Utility functions and helpers
- **integration/**: Third-party service integrations

## Renderer Process (`src/renderer/src/`)

- **pages/**: Route-based page components (home, settings, agents, etc.)
- **components/**: Reusable UI components organized by feature
- **hooks/**: Custom React hooks for state and side effects
- **services/**: Client-side services and API layers
- **store/**: Redux store configuration and slices
- **i18n/**: Internationalization setup and translations
- **types/**: TypeScript type definitions
- **utils/**: Client-side utility functions

## Workspace Packages (`packages/`)

- **shared/**: Common types and utilities shared between processes
- **mcp-trace/**: Model Context Protocol tracing functionality
  - **trace-core/**: Core tracing logic
  - **trace-node/**: Node.js tracing implementation
  - **trace-web/**: Web/renderer tracing implementation

## Key Conventions

- **File Naming**: PascalCase for components, camelCase for utilities
- **Import Aliases**: Use `@main`, `@renderer`, `@shared` path aliases
- **Service Pattern**: Singleton services with dependency injection
- **Component Structure**: Feature-based organization with co-located tests
- **Type Safety**: Strict TypeScript with explicit return types for public APIs

## Configuration Files

- **electron.vite.config.ts**: Build configuration for all processes
- **tsconfig.json**: Root TypeScript configuration with project references
- **package.json**: Workspace configuration with build scripts
- **.prettierrc**: Code formatting rules (120 char width, no semicolons)
- **eslint.config.mjs**: Linting rules with custom logger restrictions

## Documentation Structure

- **docs/technical/**: Technical implementation guides
- **docs/features/**: Feature-specific documentation
- **README.md**: Multi-language project overview
- **CONTRIBUTING.md**: Contributor guidelines and processes
