# Just Built IDE - Documentation

## Overview

Just Built IDE is an advanced web-based integrated development environment that leverages AI to streamline the application development process. This documentation covers the setup, configuration, and usage of Just Built IDE with Netlify DB powered by Neon.

## Table of Contents

1. [Architecture](#architecture)
2. [Setup and Installation](#setup-and-installation)
3. [Configuration](#configuration)
4. [Development Workflow](#development-workflow)
5. [Deployment](#deployment)
6. [API Reference](#api-reference)
7. [Troubleshooting](#troubleshooting)

## Architecture

Just Built IDE uses a modern serverless architecture with the following components:

- **Frontend**: React application deployed on Netlify
- **Backend**: Netlify Functions for serverless API endpoints
- **Database**: Netlify DB powered by Neon (serverless PostgreSQL)
- **External Services**: Integration with LLM providers, GitHub, and other APIs

The architecture is designed to be fully serverless, eliminating the need for traditional backend servers while providing robust database capabilities through Netlify DB.

## Setup and Installation

### Prerequisites

- Node.js v20.12.2 or later
- Netlify CLI
- Git

### Installation Steps

1. **Clone the repository**

```bash
git clone https://github.com/your-org/just-built-ide.git
cd just-built-ide
```

2. **Install dependencies**

```bash
# Install frontend dependencies
cd frontend/just-built-frontend
npm install

# Install backend dependencies
cd ../../netlify/functions
npm install
```

3. **Set up Netlify DB**

```bash
# Install Netlify CLI if not already installed
npm install -g netlify-cli

# Link your project to Netlify
netlify link

# Install the Netlify DB package
npm install @netlify/neon

# Initialize the database
npx netlify db init
```

4. **Start the development server**

```bash
# Start the Netlify development server
netlify dev
```

## Configuration

### Environment Variables

Create a `.env` file in the project root with the following variables:

```
# Netlify DB is automatically configured via the @netlify/neon package

# LLM API Keys
OPENAI_API_KEY=your_openai_api_key
MISTRAL_API_KEY=your_mistral_api_key
GROQ_API_KEY=your_groq_api_key

# GitHub Integration
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret

# Security
JWT_SECRET=your_jwt_secret
SETUP_TOKEN=your_setup_token
```

### Netlify Configuration

Update your `netlify.toml` file:

```toml
[build]
  command = "npm run build"
  publish = "frontend/just-built-frontend/build"
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

# Ensure Node.js version is compatible with Netlify DB
[build.environment]
  NODE_VERSION = "20.12.2"
```

## Development Workflow

### Database Schema

Just Built IDE uses the following database schema:

- **users**: User accounts and authentication
- **profiles**: User profile information
- **projects**: User projects
- **project_files**: Files within projects
- **ai_personas**: Custom AI developer personas
- **knowledge_nodes**: Knowledge graph nodes
- **knowledge_relations**: Relations between knowledge nodes
- **timeline_branches**: Development timeline branches
- **timeline_nodes**: Steps within timeline branches
- **sketches**: Visual design sketches
- **sketch_elements**: Elements within sketches

### Database Initialization

The database schema is automatically created when you run:

```bash
netlify functions:invoke db-setup --payload '{"seedData": true}'
```

This will create all necessary tables and indexes, and optionally seed the database with sample data.

### Frontend Development

The frontend is built with React and uses the following key libraries:

- Monaco Editor for code editing
- React Resizable Panels for the IDE layout
- Chakra UI for the component library
- Axios for API communication

To start frontend development:

```bash
cd frontend/just-built-frontend
npm start
```

### Backend Development

Backend functionality is implemented using Netlify Functions. Each function is a separate file in the `netlify/functions` directory.

To create a new function:

1. Create a new file in `netlify/functions`, e.g., `my-function.js`
2. Implement the function using the Netlify Functions format
3. Test the function locally using `netlify dev`

### API Client

The frontend communicates with the backend using the API client defined in `frontend/just-built-frontend/src/services/api.js`. This client handles authentication, error handling, and provides methods for all API endpoints.

## Deployment

### Deploying to Netlify

1. **Push your code to GitHub**

2. **Connect your repository to Netlify**
   - Log in to Netlify
   - Click "New site from Git"
   - Select your repository
   - Configure build settings:
     - Build command: `npm run build`
     - Publish directory: `frontend/just-built-frontend/build`

3. **Configure environment variables**
   - Add all required environment variables in the Netlify dashboard

4. **Deploy the site**
   - Netlify will automatically build and deploy your site
   - The `@netlify/neon` package will automatically provision a Netlify DB instance

5. **Claim your database**
   - Go to the Netlify dashboard
   - Navigate to Extensions > Neon database
   - Click "Claim database" to ensure it persists beyond the 7-day trial period

### Production Considerations

- **Database Scaling**: After claiming your database, you can scale it through the Neon console
- **Function Scaling**: Netlify Functions automatically scale based on demand
- **Environment Variables**: Ensure all production environment variables are set in the Netlify dashboard
- **Monitoring**: Set up monitoring for your application using Netlify Analytics and Neon monitoring tools

## API Reference

### Authentication API

- `POST /auth-register`: Register a new user
- `POST /auth-login`: Log in a user
- `GET /auth-user`: Get current user profile
- `PUT /auth-profile`: Update user profile

### Projects API

- `GET /projects`: Get all projects for current user
- `GET /project/:id`: Get a specific project
- `POST /project`: Create a new project
- `PUT /project/:id`: Update a project
- `DELETE /project/:id`: Delete a project

### Files API

- `GET /project/:id/files`: Get all files for a project
- `GET /project/:id/file/:fileId`: Get a specific file
- `POST /project/:id/file`: Create a new file
- `PUT /project/:id/file/:fileId`: Update a file
- `DELETE /project/:id/file/:fileId`: Delete a file

### AI Personas API

- `GET /personas`: Get all AI personas for current user
- `GET /persona/:id`: Get a specific persona
- `POST /persona`: Create a new persona
- `PUT /persona/:id`: Update a persona
- `DELETE /persona/:id`: Delete a persona

### LLM API

- `POST /llm/generate-code`: Generate code from prompt
- `POST /llm/generate-plan`: Generate plan from requirements
- `POST /llm/execute-step/:id`: Execute a step in the plan
- `GET /llm/models`: Get available LLM models

### GitHub API

- `POST /github/connect`: Connect GitHub account
- `GET /github/repositories`: Get user repositories
- `POST /github/import`: Import project from repository
- `POST /github/export/:id`: Export project to repository

### Build API

- `POST /build/local/:id`: Build project for local use
- `POST /build/web/:id`: Build project for web use
- `GET /build/status/:id`: Get build status
- `GET /build/logs/:id`: Get build logs
- `GET /build/download/:id`: Download build artifacts

## Troubleshooting

### Common Issues

#### Database Connection Issues

If you encounter database connection issues:

1. Verify that the `@netlify/neon` package is installed
2. Check that you're using Node.js v20.12.2 or later
3. Run `netlify dev --debug` to see detailed logs
4. Verify that the database has been provisioned by checking the Netlify dashboard

#### Authentication Issues

If users cannot log in:

1. Check that the JWT_SECRET environment variable is set
2. Verify that the authentication functions are deployed correctly
3. Check browser console for any CORS errors
4. Ensure the user exists in the database

#### Deployment Issues

If deployment fails:

1. Check the Netlify build logs for errors
2. Verify that all environment variables are set correctly
3. Ensure the build command and publish directory are configured correctly
4. Check that the Node.js version is set to 20.12.2 or later

### Getting Help

If you need additional help:

- Check the [GitHub Issues](https://github.com/your-org/just-built-ide/issues) for known problems
- Join our [Discord community](https://discord.gg/justbuilt) for support
- Contact us at support@justbuilt.dev

## License

Just Built IDE is licensed under the MIT License. See the LICENSE file for details.
