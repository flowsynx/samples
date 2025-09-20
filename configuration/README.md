# Configuration Overview

This directory contains modular configuration files for the FlowSynx application.
Each subfolder represents a distinct configuration area, making it easy to enable, disable, or customize individual features without affecting the rest of the system.

Configuration files follow the standard `appsettings.json` format and are automatically loaded by the FlowSynx runtime.
These settings can be overridden by environment variables or command-line arguments, allowing for seamless deployment across different environments (development, staging, production).

## Folder Summary

| Folder                                              | Purpose                                                                                                                                                  |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**HealthCheck**](/configuration/health-check/)     | Defines application health-check probes, such as liveness and readiness endpoints. Useful for Kubernetes, Docker, and container orchestration platforms. |
| [**Endpoints**](/configuration/endpoints/)          | Configures HTTP endpoint ports, server URLs, and optional HTTPS/TLS settings. Supports binding to multiple interfaces.                                   |
| [**Cors**](/configuration/cors/)                    | Manages **Cross-Origin Resource Sharing (CORS)** policies. Controls which external domains can access FlowSynx APIs.                                     |
| [**Localization**](/configuration/localization/)    | Provides language and locale settings. Currently supports English (`en`) by default, with placeholders for future multilingual expansion.                |
| [**RateLimiting**](/configuration/rate-limiting/)   | Enables API request throttling to protect against abuse or accidental traffic spikes. Supports per-user, per-endpoint, and global limits.                |
| [**WorkflowQueue**](/configuration/workflow-queue/) | Specifies the workflow queue provider (e.g., **InMemory**, **Durable**). Controls retry policies and message durability.         |
| [**Basic\_Auth**](/configuration/basic-auth/)       | Defines static user accounts, roles, and credentials for basic authentication. Useful for local development or simple deployments.                       |
| [**Keycloak\_Auth**](/configuration/keycloak_auth/) | Integrates with **Keycloak** or other OpenID Connect providers for secure authentication and role-based access control. Recommended for production.      |
