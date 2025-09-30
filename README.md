# FlowSynx Samples and Quickstarts 
This repository contains configuration files and workflows for various application features. It is organized into directories based on functionality, making it easy to locate and manage specific configurations.

> If you are new to FlowSynx and haven't done so already, it is recommended you go through the FlowSynx [Getting Started](https://flowsynx.io/docs/getting-started) instructions.

## Directory Structure

### Configuration
Each subfolder represents a distinct configuration area, making it easy to enable, disable, or customize individual features without affecting the rest of the system. The `configuration/` directory contains configuration files for different FlowSynx features. Each subdirectory represents a distinct configuration area, making it easy to enable, disable, or customize individual features without affecting the rest of the system:

| Directory                                              | Purpose                                                                                                                                                  |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**HealthCheck**](/configuration/health-check/)     | Defines application health-check probes, such as liveness and readiness endpoints. Useful for Kubernetes, Docker, and container orchestration platforms. |
| [**Endpoints**](/configuration/endpoints/)          | Configures HTTP endpoint ports, server URLs, and optional HTTPS/TLS settings. Supports binding to multiple interfaces.                                   |
| [**Cors**](/configuration/cors/)                    | Manages **Cross-Origin Resource Sharing (CORS)** policies. Controls which external domains can access FlowSynx APIs.                                     |
| [**Localization**](/configuration/localization/)    | Provides language and locale settings. Currently supports English (`en`) by default, with placeholders for future multilingual expansion.                |
| [**RateLimiting**](/configuration/rate-limiting/)   | Enables API request throttling to protect against abuse or accidental traffic spikes. Supports per-user, per-endpoint, and global limits.                |
| [**WorkflowQueue**](/configuration/workflow-queue/) | Specifies the workflow queue provider (e.g., **InMemory**, **Durable**). Controls retry policies and message durability.         |
| [**Basic\_Auth**](/configuration/basic-auth/)       | Defines static user accounts, roles, and credentials for basic authentication. Useful for local development or simple deployments.                       |
| [**Keycloak\_Auth**](/configuration/keycloak_auth/) | Integrates with **Keycloak** or other OpenID Connect providers for secure authentication and role-based access control. Recommended for production.      |

### Workflows
The `workflows/` directory stores predefined workflow configurations for various use cases:

| Directory   | Description                     |
|-------------|---------------------------------|
| [**Basic**](/workflows/basic/)  | Includes minimal JSON-based workflow definition, such as a simple example that writes text to a file. |
| [**NightlyDatabaseBackup**](/workflows/nightly-database-backup/)  | Includes JSON-based workflow definition for Dump PostgreSQL database, compress, and store it to Azure Blob Storage. |
| [**WeatherDataETL**](/workflows/weather-data-etl/)  | Fetch hourly weather JSON, normalize, insert into PostgreSQL. |