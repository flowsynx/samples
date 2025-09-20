# Samples

This repository contains configuration files and workflows for various application features. It is organized into directories based on functionality, making it easy to locate and manage specific configurations.

## Directory Structure

### Configuration
The `configuration/` directory contains configuration files for different application features. Each subdirectory includes its own `appsettings.json` file or related configuration files:

- **basic-auth/**: Configuration for basic authentication.
- **cors/**: Configuration for Cross-Origin Resource Sharing (CORS).
- **endpoints/**: Configuration for HTTP and HTTPS endpoints.
  - `appsettings-http.json`
  - `appsettings-https.json`
- **health-check/**: Configuration for health checks.
- **Keycloak_Auth/**: Configuration for Keycloak authentication.
- **localization/**: Configuration for localization settings.
- **rate-limiting/**: Configuration for rate-limiting policies.
- **workflow-queue/**: Configuration for workflow queue management.
  - `appsettings-durable.json`
  - `appsettings-memory.json`

### Workflows
The `workflows/` directory contains workflow definitions for different scenarios:

- **basic/**: Contains basic workflow definitions.
  - `workflow.json`