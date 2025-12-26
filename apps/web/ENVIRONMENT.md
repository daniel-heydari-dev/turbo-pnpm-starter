# Environment Configuration

## Overview

This document explains how environment variables are configured for the web application.

## The Problem

Vite has a specific order of operations when starting:

1. **First**: Vite reads and evaluates `vite.config.js`
2. **Then**: Vite loads `.env` files and makes `VITE_*` variables available to the client

This means that when `vite.config.js` is being executed, `process.env.VITE_PORT` is still `undefined` because the `.env` file hasn't been loaded yet!

## The Solution

We use Vite's built-in `loadEnv` helper to explicitly load environment variables **before** they're needed in the config:

```javascript
import { defineConfig, loadEnv } from 'vite';

export default defineConfig(({ mode }) => {
    // Load all env variables (the '' prefix means load ALL, not just VITE_*)
    const env = loadEnv(mode, process.cwd(), '');

    return {
        server: {
            port: Number(env.VITE_PORT) || 3000,
        },
    };
});
```

### How `loadEnv` Works

- **`mode`**: The current mode (e.g., `development` or `production`)
- **`process.cwd()`**: Directory to search for `.env` files
- **`''` (empty string)**: Prefix filter - empty string loads ALL variables, not just `VITE_*` prefixed ones

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `VITE_PORT` | Development server port | `3000` |

## Usage

1. Create a `.env` file in `apps/web/`:
   ```
   VITE_PORT=3500
   ```

2. Run the dev server:
   ```bash
   pnpm dev:web
   ```

3. The app will now be available at `http://localhost:3500/`

## Important Notes

- `.env` files are **git-ignored** for security
- Use `.env.example` as a template for required variables
- Variables prefixed with `VITE_` are exposed to the client-side code
- Non-prefixed variables are only available in the config/server
