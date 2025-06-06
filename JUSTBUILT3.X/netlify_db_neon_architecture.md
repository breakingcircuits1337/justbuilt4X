# Just Built IDE - Netlify DB and Neon Architecture

## System Architecture Overview

This document outlines the updated architecture for Just Built IDE, leveraging Netlify DB powered by Neon for database services and Netlify Functions for serverless backend operations. This modern architecture eliminates the need for a separate backend service while providing robust database capabilities optimized for AI workflows.

## Architecture Diagram

```
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│                     Client Browser                            │
│                                                               │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                ▼
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│                 Netlify Platform                              │
│                                                               │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐│
│  │                 │  │                 │  │                 ││
│  │  React App      │  │  Netlify        │  │  Asset          ││
│  │  (Just Built)   │  │  Functions      │  │  Delivery (CDN) ││
│  │                 │  │                 │  │                 ││
│  └─────────────────┘  └────────┬────────┘  └─────────────────┘│
│                                │                              │
│  ┌─────────────────────────────┴─────────────────────────┐    │
│  │                                                       │    │
│  │                    Netlify DB                         │    │
│  │                  (Powered by Neon)                    │    │
│  │                                                       │    │
│  └───────────────────────────────────────────────────────┘    │
│                                                               │
└────────────────────────────────┬──────────────────────────────┘
                                 │
                                 ▼
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│                 External Services                             │
│                                                               │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐│
│  │                 │  │                 │  │                 ││
│  │  LLM Providers  │  │  GitHub API     │  │  Other APIs     ││
│  │  (OpenAI, etc.) │  │                 │  │                 ││
│  └─────────────────┘  └─────────────────┘  └─────────────────┘│
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

## Component Architecture

### 1. Frontend Architecture (Netlify)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│                       React Application                                 │
│                                                                         │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────┐  │
│  │                 │  │                 │  │                         │  │
│  │  UI Components  │  │  State          │  │  API Client Layer       │  │
│  │                 │  │  Management     │  │                         │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────────────┘  │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                                                                 │    │
│  │                     Feature Modules                             │    │
│  │                                                                 │    │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────┐ │    │
│  │  │             │  │             │  │             │  │         │ │    │
│  │  │ AI Personas │  │ Knowledge   │  │ Time-Travel │  │ Visual  │ │    │
│  │  │             │  │ Graph       │  │ Development │  │ to Code │ │    │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────┘ │    │
│  │                                                                 │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  Monaco Editor          │  │  Service Layer                      │   │
│  │  Integration            │  │  (Utilities, Helpers)               │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2. Netlify Functions Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│                       Netlify Functions                                 │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  Database Operations    │  │  LLM Proxy Functions                │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  GitHub Integration     │  │  File Operations                    │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  Authentication         │  │  Build & Deploy                     │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3. Netlify DB Schema (Postgres)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│                       Database Schema                                   │
│                                                                         │
│  ┌─────────────────────────┐       ┌─────────────────────────────────┐  │
│  │ users                   │       │ projects                        │  │
│  │─────────────────────────│       │─────────────────────────────────│  │
│  │ id            UUID PK   │       │ id            UUID PK           │  │
│  │ email         TEXT      │◄──────┤ user_id       UUID FK           │  │
│  │ created_at    TIMESTAMP │       │ name          TEXT              │  │
│  │ updated_at    TIMESTAMP │       │ description   TEXT              │  │
│  └─────────────────────────┘       │ created_at    TIMESTAMP         │  │
│           ▲                        │ updated_at    TIMESTAMP         │  │
│           │                        └─────────────────────────────────┘  │
│           │                                      │                      │
│  ┌────────┴──────────────┐                       │                      │
│  │ profiles              │                       │                      │
│  │──────────────────────-│                       │                      │
│  │ id         UUID PK    │                       │                      │
│  │ user_id    UUID FK    │                       │                      │
│  │ full_name  TEXT       │                       │                      │
│  │ avatar_url TEXT       │                       │                      │
│  └─────────────────────────┘                     ▼                      │
│                                  ┌─────────────────────────────────┐    │
│  ┌─────────────────────────┐     │ project_files                   │    │
│  │ ai_personas             │     │─────────────────────────────────│    │
│  │─────────────────────────│     │ id            UUID PK           │    │
│  │ id            UUID PK   │     │ project_id    UUID FK           │    │
│  │ user_id       UUID FK   │     │ name          TEXT              │    │
│  │ name          TEXT      │     │ content       TEXT              │    │
│  │ description   TEXT      │     │ path          TEXT              │    │
│  │ traits        JSONB     │     │ created_at    TIMESTAMP         │    │
│  │ expertise     JSONB     │     │ updated_at    TIMESTAMP         │    │
│  │ coding_style  JSONB     │     └─────────────────────────────────┘    │
│  └─────────────────────────┘                                            │
│                                                                         │
│  ┌─────────────────────────┐     ┌─────────────────────────────────┐    │
│  │ knowledge_nodes         │     │ knowledge_relations             │    │
│  │─────────────────────────│     │─────────────────────────────────│    │
│  │ id            UUID PK   │     │ id            UUID PK           │    │
│  │ type          TEXT      │     │ source_id     UUID FK           │    │
│  │ name          TEXT      │     │ target_id     UUID FK           │    │
│  │ description   TEXT      │     │ type          TEXT              │    │
│  │ metadata      JSONB     │     │ strength      INTEGER           │    │
│  └─────────────────────────┘     │ description   TEXT              │    │
│                                  └─────────────────────────────────┘    │
│                                                                         │
│  ┌─────────────────────────┐     ┌─────────────────────────────────┐    │
│  │ timeline_branches       │     │ timeline_nodes                  │    │
│  │─────────────────────────│     │─────────────────────────────────│    │
│  │ id            UUID PK   │     │ id            UUID PK           │    │
│  │ project_id    UUID FK   │     │ branch_id     UUID FK           │    │
│  │ name          TEXT      │     │ type          TEXT              │    │
│  │ description   TEXT      │     │ title         TEXT              │    │
│  │ is_active     BOOLEAN   │     │ content       TEXT              │    │
│  │ parent_id     UUID FK   │     │ parent_id     UUID FK           │    │
│  └─────────────────────────┘     │ step_number   INTEGER           │    │
│                                  └─────────────────────────────────┘    │
│                                                                         │
│  ┌─────────────────────────┐     ┌─────────────────────────────────┐    │
│  │ sketches                │     │ sketch_elements                 │    │
│  │─────────────────────────│     │─────────────────────────────────│    │
│  │ id            UUID PK   │     │ id            UUID PK           │    │
│  │ project_id    UUID FK   │     │ sketch_id     UUID FK           │    │
│  │ name          TEXT      │     │ type          TEXT              │    │
│  │ width         INTEGER   │     │ x             FLOAT             │    │
│  │ height        INTEGER   │     │ y             FLOAT             │    │
│  │ background    TEXT      │     │ width         FLOAT             │    │
│  └─────────────────────────┘     │ height        FLOAT             │    │
│                                  │ properties    JSONB             │    │
│                                  │ parent_id     UUID FK           │    │
│                                  └─────────────────────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Integration Points

### 1. Frontend to Netlify Functions Integration

The React frontend will communicate with Netlify Functions using standard HTTP requests:

```typescript
// Example API client in the frontend
const api = {
  async getProjects() {
    const response = await fetch('/.netlify/functions/get-projects');
    if (!response.ok) throw new Error('Failed to fetch projects');
    return response.json();
  },
  
  async createProject(projectData) {
    const response = await fetch('/.netlify/functions/create-project', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(projectData)
    });
    if (!response.ok) throw new Error('Failed to create project');
    return response.json();
  }
  
  // Additional API methods...
}
```

### 2. Netlify Functions to Database Integration

Netlify Functions will connect to Netlify DB using the automatically provisioned environment variables:

```javascript
// Example Netlify Function with database access
const { Pool } = require('pg');

// Netlify DB automatically provides the DATABASE_URL environment variable
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});

exports.handler = async function(event, context) {
  // Connect to the database
  const client = await pool.connect();
  
  try {
    // Query the database
    const result = await client.query('SELECT * FROM projects WHERE user_id = $1', [event.user_id]);
    
    // Return the results
    return {
      statusCode: 200,
      body: JSON.stringify(result.rows)
    };
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  } finally {
    // Release the client back to the pool
    client.release();
  }
};
```

### 3. Netlify Functions to External APIs Integration

Netlify Functions will also handle communication with external services like LLM providers:

```javascript
// Example Netlify Function for LLM integration
const axios = require('axios');

exports.handler = async function(event, context) {
  try {
    const { prompt, model } = JSON.parse(event.body);
    
    const response = await axios.post(
      'https://api.openai.com/v1/completions',
      {
        model: model || 'gpt-4',
        prompt,
        max_tokens: 2048
      },
      {
        headers: {
          'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    return {
      statusCode: 200,
      body: JSON.stringify(response.data)
    };
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
```

### 4. Database Schema Implementation

Using Drizzle ORM (recommended by Netlify DB) for database schema management:

```typescript
// Example schema definition with Drizzle ORM
import { pgTable, uuid, text, timestamp, jsonb, integer, boolean } from 'drizzle-orm/pg-core';

// Users table
export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').notNull().unique(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull()
});

// Projects table
export const projects = pgTable('projects', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id).notNull(),
  name: text('name').notNull(),
  description: text('description'),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull()
});

// AI Personas table
export const aiPersonas = pgTable('ai_personas', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id).notNull(),
  name: text('name').notNull(),
  description: text('description'),
  traits: jsonb('traits'),
  expertise: jsonb('expertise'),
  codingStyle: jsonb('coding_style'),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull()
});

// Additional tables for other features...
```

## Deployment Configuration

### Netlify Configuration

The `netlify.toml` configuration file:

```toml
[build]
  command = "npm run build"
  publish = "build"
  functions = "netlify/functions"

[dev]
  command = "npm start"
  port = 3000
  targetPort = 3000

[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[functions]
  node_bundler = "esbuild"

# Automatically provision Netlify DB
[build.environment]
  NODE_VERSION = "20.12.2"
```

### Package.json Configuration

```json
{
  "name": "just-built-ide",
  "version": "1.0.0",
  "dependencies": {
    "@netlify/neon": "^1.0.0",
    "drizzle-orm": "^0.28.0",
    "pg": "^8.11.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "@monaco-editor/react": "^4.5.0",
    "axios": "^1.4.0"
    // Additional dependencies...
  },
  "scripts": {
    "start": "netlify dev",
    "build": "react-scripts build",
    "db:init": "netlify db init --boilerplate=drizzle",
    "db:push": "drizzle-kit push:pg"
    // Additional scripts...
  }
}
```

## Security Considerations

### Authentication Security
- Netlify Identity for user authentication
- JWT-based authentication with proper expiration
- Secure token storage in browser

### Data Security
- Postgres row-level security policies
- Encrypted data at rest (Neon default)
- TLS for all data in transit
- Proper permission scoping

### API Security
- Rate limiting for API endpoints
- Input validation and sanitization
- CORS configuration
- API key rotation policies

### Frontend Security
- Content Security Policy implementation
- XSS prevention measures
- CSRF protection
- Secure cookie handling

## Performance Considerations

### Frontend Performance
- Code splitting and lazy loading
- Asset optimization via Netlify CDN
- Efficient state management
- Virtualized rendering for large datasets

### Database Performance
- Proper indexing strategy
- Query optimization
- Connection pooling
- Efficient JSON handling

### API Performance
- Response caching
- Batch operations
- Efficient pagination
- Optimized data fetching

## Deployment Strategy

### Initial Setup
1. Create a new Netlify project
2. Install the `@netlify/neon` package
3. Run `netlify db init` to provision the database
4. Deploy the application to Netlify
5. Claim the database for production use

### Continuous Integration/Deployment
- GitHub Actions for CI/CD
- Automated testing before deployment
- Preview deployments for pull requests
- Staged rollouts for production

### Environment Management
- Development, staging, and production environments
- Environment-specific configurations
- Feature flags for controlled rollouts
- Monitoring and alerting

## Conclusion

This architecture leverages Netlify DB powered by Neon to create a modern, serverless application with robust database capabilities. By eliminating the need for a separate backend service, we simplify the architecture while maintaining all the required functionality for the Just Built IDE.

The integration with Netlify Functions provides a powerful serverless backend, while the automatic database provisioning through the `@netlify/neon` package makes it ideal for AI-driven development workflows. This architecture provides a solid foundation for the Just Built IDE, enabling rapid development and deployment while maintaining flexibility for future enhancements.
