# Just Built IDE - Supabase and Netlify Architecture

## System Architecture Overview

This document outlines the updated architecture for Just Built IDE, integrating Supabase for backend services and Netlify for frontend deployment. This modern serverless architecture provides scalability, robust authentication, and simplified deployment.

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
│                 Netlify (Frontend Hosting)                    │
│                                                               │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐│
│  │                 │  │                 │  │                 ││
│  │  React App      │  │  Netlify        │  │  Asset          ││
│  │  (Just Built)   │  │  Functions      │  │  Delivery (CDN) ││
│  │                 │  │                 │  │                 ││
│  └─────────────────┘  └────────┬────────┘  └─────────────────┘│
│                                │                              │
└────────────────────────────────┼──────────────────────────────┘
                                 │
                                 ▼
┌────────────────────────────────────────────────────────────────────┐
│                                                                    │
│                         Supabase Platform                          │
│                                                                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────┐ │
│  │                 │  │                 │  │                     │ │
│  │  Auth Service   │  │  PostgreSQL     │  │  Storage            │ │
│  │                 │  │  Database       │  │                     │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────────┘ │
│                                                                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────┐ │
│  │                 │  │                 │  │                     │ │
│  │  Realtime       │  │  Edge Functions │  │  PostgREST API      │ │
│  │                 │  │                 │  │                     │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────────┘ │
│                                                                    │
└───────────────────────────────┬────────────────────────────────────┘
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
│  │  UI Components  │  │  State          │  │  Supabase Client        │  │
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
│  │  Integration            │  │  (API Clients, Utilities)           │   │
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
│  │  LLM Proxy Functions    │  │  GitHub Integration Functions       │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  Build Functions        │  │  Utility Functions                  │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3. Supabase Database Schema

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

### 4. Supabase Storage Buckets

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│                       Storage Buckets                                   │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  project-assets         │  │  user-uploads                       │   │
│  │  (Project files)        │  │  (User uploaded files)              │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────┐  ┌─────────────────────────────────────┐   │
│  │                         │  │                                     │   │
│  │  generated-code         │  │  sketch-images                      │   │
│  │  (Generated code files) │  │  (Sketch references and exports)    │   │
│  │                         │  │                                     │   │
│  └─────────────────────────┘  └─────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────┐                                            │
│  │                         │                                            │
│  │  profile-avatars        │                                            │
│  │  (User profile images)  │                                            │
│  │                         │                                            │
│  └─────────────────────────┘                                            │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Data Flow Diagrams

### 1. Authentication Flow

```
┌──────────┐     1. Login Request     ┌──────────┐
│          │─────────────────────────▶│          │
│  React   │                          │ Supabase │
│  App     │◀─────────────────────────│ Auth     │
│          │     2. JWT Token         │          │
└──────────┘                          └──────────┘
     │                                     ▲
     │ 3. Store Token                      │
     ▼                                     │
┌──────────┐     4. API Requests     ┌──────────┐
│          │─────────────────────────▶│          │
│ Browser  │                          │ Supabase │
│ Storage  │                          │ API      │
│          │                          │          │
└──────────┘                          └──────────┘
```

### 2. Project Creation Flow

```
┌──────────┐  1. Create Project   ┌──────────┐
│          │────────────────────▶ │          │
│  React   │                      │ Supabase │
│  App     │◀────────────────────-│ Database │
│          │  2. Project Data     │          │
└──────────┘                      └──────────┘
     │                                 │
     │ 3. Initialize                   │ 4. Store Files
     ▼                                 ▼
┌──────────┐                     ┌──────────┐
│          │                     │          │
│ Project  │                     │ Supabase │
│ Editor   │                     │ Storage  │
│          │                     │          │
└──────────┘                     └──────────┘
```

### 3. LLM Integration Flow

```
┌──────────┐  1. Code Request   ┌───────────────┐
│          │───────────────────▶│               │
│  React   │                    │ Netlify       │
│  App     │◀───────────────────│ Function      │
│          │  4. Generated Code │               │
└──────────┘                    └───────────────┘
                                       │
                                       │ 2. LLM API Call
                                       ▼
                                ┌───────────────┐
                                │               │
                                │ LLM Provider  │
                                │ (OpenAI, etc) │
                                │               │
                                └───────────────┘
                                       │
                                       │ 3. Response
                                       ▼
                                ┌───────────────┐
                                │               │
                                │ Supabase      │
                                │ Database      │
                                │               │
                                └───────────────┘
```

### 4. Real-time Collaboration Flow

```
┌──────────┐  1. Subscribe      ┌───────────────┐
│          │───────────────────▶│               │
│  User A  │                    │ Supabase      │
│  Browser │◀───────────────────│ Realtime      │
│          │  4. Updates        │               │
└──────────┘                    └───────────────┘
                                       ▲
                                       │ 2. Changes
                                       │
┌──────────┐  3. Publish       ┌───────────────┐
│          │───────────────────▶│               │
│  User B  │                    │ Supabase      │
│  Browser │                    │ Database      │
│          │                    │               │
└──────────┘                    └───────────────┘
```

## Integration Points

### 1. Frontend to Supabase Integration

The React frontend will integrate with Supabase using the official Supabase JavaScript client:

```typescript
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  'https://your-project.supabase.co',
  'your-anon-key'
)

// Authentication
const signIn = async (email, password) => {
  const { user, error } = await supabase.auth.signIn({ email, password })
  return { user, error }
}

// Database operations
const getProjects = async () => {
  const { data, error } = await supabase
    .from('projects')
    .select('*')
    .eq('user_id', supabase.auth.user().id)
  
  return { data, error }
}

// Storage operations
const uploadFile = async (bucket, path, file) => {
  const { data, error } = await supabase
    .storage
    .from(bucket)
    .upload(path, file)
  
  return { data, error }
}

// Realtime subscriptions
const subscribeToChanges = (tableName, callback) => {
  const subscription = supabase
    .from(tableName)
    .on('*', payload => {
      callback(payload)
    })
    .subscribe()
  
  return subscription
}
```

### 2. Netlify Functions to External APIs

Netlify Functions will handle complex operations and external API calls:

```javascript
// netlify/functions/llm-proxy.js
const axios = require('axios')

exports.handler = async function(event, context) {
  try {
    const { prompt, model } = JSON.parse(event.body)
    
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
    )
    
    return {
      statusCode: 200,
      body: JSON.stringify(response.data)
    }
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    }
  }
}
```

### 3. Supabase Row-Level Security Policies

Security policies to ensure proper data access:

```sql
-- Projects table policy
CREATE POLICY "Users can view their own projects" 
ON projects FOR SELECT 
USING (auth.uid() = user_id);

CREATE POLICY "Users can insert their own projects" 
ON projects FOR INSERT 
WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own projects" 
ON projects FOR UPDATE 
USING (auth.uid() = user_id);

-- Project files policy
CREATE POLICY "Users can view their project files" 
ON project_files FOR SELECT 
USING (
  auth.uid() IN (
    SELECT user_id FROM projects WHERE id = project_files.project_id
  )
);

-- Similar policies for other tables
```

### 4. Netlify Configuration

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

[build.environment]
  REACT_APP_SUPABASE_URL = "https://your-project.supabase.co"
  REACT_APP_SUPABASE_ANON_KEY = "your-anon-key"
```

## Security Considerations

### Authentication Security
- JWT-based authentication with proper expiration
- Secure token storage in browser
- PKCE flow for OAuth providers
- MFA support via Supabase Auth

### Data Security
- Row-level security policies for all tables
- Encrypted data at rest (Supabase default)
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

### Storage Performance
- CDN distribution for assets
- Image optimization
- Efficient file chunking
- Progressive loading

## Deployment Strategy

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

### Backup and Recovery
- Regular database backups
- Point-in-time recovery
- Disaster recovery plan
- Data migration procedures

## Conclusion

This architecture leverages the strengths of Supabase and Netlify to create a modern, scalable, and maintainable application. The serverless approach eliminates infrastructure management concerns while providing robust features for authentication, data storage, and real-time capabilities.

The integration points are clearly defined, with security and performance considerations addressed throughout the design. This architecture provides a solid foundation for the Just Built IDE, enabling rapid development and deployment while maintaining flexibility for future enhancements.
