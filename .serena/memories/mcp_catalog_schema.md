# MCP Catalog YAML Schema Reference

## Overview

Each YAML file in the root directory represents an MCP (Model Context Protocol) server entry in the catalog. These files define how to connect to and interact with various MCP servers.

## Schema Structure

### Required Fields

#### Basic Information

```yaml
name: String
  # Display name of the MCP server (e.g., "GitHub", "Slack", "Notion")

shortDescription: String
  # One-line description (under ~100 characters)

description: |
  # Multi-line detailed description
  # Should include:
  # - What the server does
  # - Key features (bulleted list)
  # - What you'll need to connect
  # - Available toolsets/endpoints (if applicable)

icon: URL
  # Direct URL to server icon/logo
  # Usually from GitHub avatars or official source

repoURL: URL
  # GitHub repository URL for the MCP server
  # Format: https://github.com/org/repo
```

#### Runtime Configuration

```yaml
runtime: remote|containerized|local
  # Specifies how the MCP server is deployed/run
  # - remote: Hosted service accessed via HTTP
  # - containerized: Docker container
  # - local: Runs on local machine
```

### Runtime-Specific Configurations

#### Remote Configuration

For `runtime: remote`:

```yaml
remoteConfig:
  hostname: String
    # API endpoint hostname (e.g., "api.githubcopilot.com")

  headers:
    - name: String
        # Display name for the header (e.g., "Personal Access Token")
      description: String
        # Description of what this header is for
      key: String
        # Actual HTTP header name (e.g., "Authorization")
      required: Boolean
        # Whether this header is mandatory
      sensitive: Boolean
        # If true, value will be treated as secret/credential
```

**Example (GitHub)**:

```yaml
remoteConfig:
  hostname: api.githubcopilot.com
  headers:
    - name: Personal Access Token
      description: GitHub PAT
      key: Authorization
      required: true
      sensitive: true
```

#### Containerized Configuration

For `runtime: containerized`:

```yaml
containerizedConfig:
  image: String
    # Docker image name with tag (e.g., "ghcr.io/org/server:latest")

  port: Integer
    # Port the container exposes (e.g., 8080)

  path: String (optional)
    # API endpoint path (e.g., "/", "/api")

  args: Array<String> (optional)
    # Command-line arguments passed to container
    # Example: ["--verbose", "--config=/app/config.json"]
```

**Example**:

```yaml
containerizedConfig:
  image: ghcr.io/example/mcp-server:v1.0.0
  port: 8080
  path: /mcp
  args: ["--log-level=info"]
```

### Optional Fields

#### Tool Preview

```yaml
toolPreview:
  - name: String
      # Tool/function name (snake_case convention)
    description: String
      # What the tool does
    params:
      param_name: Description
        # Parameter description
        # Mark optional params with "(optional)" suffix
```

**Example**:

```yaml
toolPreview:
  - name: create_issue
    description: Create a new issue in a GitHub repository
    params:
      owner: Repository owner
      repo: Repository name
      title: Issue title
      body: Issue body content (optional)
      labels: Labels to apply to this issue (optional)
```

#### Metadata

```yaml
metadata:
  categories: String
    # Category classification (e.g., "Developer Tools", "AI & Machine Learning")
    # Can be comma-separated for multiple categories

  unsupportedTools: String (optional)
    # Comma-separated list of tools not supported
    # Usually for tools that exist but don't work in certain contexts
```

**Example**:

```yaml
metadata:
  categories: Developer Tools
  unsupportedTools: create_or_update_file,push_files
```

#### Environment Variables

```yaml
env:
  - key: String
      # Environment variable name (UPPER_SNAKE_CASE)
    description: String
      # What this variable is for
    required: Boolean
      # Whether this env var is mandatory
    sensitive: Boolean
      # If true, treat as secret (API keys, tokens, etc.)
    default: String (optional)
      # Default value if not provided
```

**Example**:

```yaml
env:
  - key: OPENAI_API_KEY
    description: OpenAI API key for AI features
    required: true
    sensitive: true
  - key: LOG_LEVEL
    description: Logging verbosity
    required: false
    sensitive: false
    default: "info"
```

## Complete Example

### Remote Server (GitHub)

```yaml
name: GitHub
shortDescription: Manage GitHub repositories, issues, PRs, workflows, and CI/CD operations
description: |
  A Model Context Protocol (MCP) server that provides easy connection to GitHub using the hosted version.
  
  ## Features
  - Repository Management
  - Issue & PR Automation
  - CI/CD & Workflow Intelligence

  ## What you'll need
  - Personal Access Token with appropriate permissions

icon: https://avatars.githubusercontent.com/u/9919?v=4
repoURL: https://github.com/github/github-mcp-server

runtime: remote
remoteConfig:
  hostname: api.githubcopilot.com
  headers:
    - name: Personal Access Token
      description: GitHub PAT
      key: Authorization
      required: true
      sensitive: true

toolPreview:
  - name: create_issue
    description: Create a new issue in a GitHub repository
    params:
      owner: Repository owner
      repo: Repository name
      title: Issue title
      body: Issue body content (optional)

metadata:
  categories: Developer Tools
  unsupportedTools: create_or_update_file,push_files
```

### Containerized Server

```yaml
name: Redis MCP Server
shortDescription: Interact with Redis database via MCP
description: |
  Connect to Redis databases and execute commands via MCP.
  
  ## Features
  - Key-value operations
  - Data structure commands
  - Database management

icon: https://redis.io/images/redis-logo.svg
repoURL: https://github.com/example/redis-mcp

runtime: containerized
containerizedConfig:
  image: ghcr.io/example/redis-mcp:latest
  port: 6379
  path: /
  args: ["--db-url", "redis://localhost:6379"]

env:
  - key: REDIS_URL
    description: Redis connection URL
    required: true
    sensitive: false
  - key: REDIS_PASSWORD
    description: Redis authentication password
    required: false
    sensitive: true

metadata:
  categories: Database
```

## Validation Rules

### YAML Syntax

- Use 2-space indentation
- Multi-line strings use `|` operator
- Boolean values: lowercase `true`/`false`
- No tabs, only spaces

### Field Validation

- All required fields must be present
- URLs must be valid and accessible
- Runtime config must match runtime type
- Sensitive fields should be marked appropriately
- Tool names should be snake_case
- Environment variable keys should be UPPER_SNAKE_CASE

### Best Practices

1. Keep `shortDescription` concise and informative
2. Use markdown formatting in `description`
3. Include setup/prerequisites in description
4. Mark all credentials/tokens as `sensitive: true`
5. Document all tool parameters clearly
6. Mark optional parameters with "(optional)" suffix
7. Use descriptive, specific names for tools
8. Test containerized configs before committing
