# Technology Stack & Build System

## Core Technologies

- **Framework**: Electron 37.2.3 with Vite-based build system
- **Frontend**: React 19 + TypeScript + Redux Toolkit + Styled Components
- **Backend**: Node.js 22+ with native modules and services
- **Database**: LibSQL for local data storage
- **Package Manager**: Yarn 4.9.1 with workspaces

## Build System

- **Build Tool**: electron-vite 4.0.0 with SWC for fast compilation
- **Bundler**: Rolldown (Vite fork) for optimized builds
- **TypeScript**: Strict configuration with decorators support
- **Testing**: Vitest with separate configs for main/renderer processes

## Key Libraries & Frameworks

- **UI Components**: Ant Design 5.26.7 (patched)
- **State Management**: Redux Toolkit with Redux Persist
- **Routing**: React Router 6 with hash-based routing
- **Styling**: Styled Components + Sass
- **Code Highlighting**: Shiki 3.9.1 with CodeMirror
- **AI Integration**: OpenAI SDK, Anthropic SDK, Google GenAI
- **Document Processing**: PDF-lib, DOCX, EmbedJS ecosystem
- **Internationalization**: i18next with React integration

## Development Commands

```bash
# Development
yarn dev              # Start development server
yarn debug           # Start with debugging (chrome://inspect)

# Building
yarn build           # Full build with type checking
yarn build:win       # Windows build (x64 + arm64)
yarn build:mac       # macOS build (x64 + arm64)
yarn build:linux     # Linux build (x64 + arm64)

# Testing
yarn test            # Run all tests
yarn test:main       # Main process tests only
yarn test:renderer   # Renderer process tests only
yarn test:e2e        # End-to-end tests with Playwright

# Code Quality
yarn lint            # ESLint + TypeScript + i18n checks
yarn format          # Prettier formatting
yarn typecheck       # TypeScript type checking

# Internationalization
yarn check:i18n      # Check i18n key consistency
yarn sync:i18n       # Sync translation keys
yarn auto:i18n       # Auto-translate missing keys
```

## Architecture Patterns

- **Process Separation**: Main process handles system integration, renderer handles UI
- **IPC Communication**: Structured message passing between processes
- **Service Layer**: Centralized services in main process (MCPService, WindowService, etc.)
- **Hook-based UI**: Custom React hooks for state management and side effects
- **Middleware Pattern**: AI core with pluggable middleware system
