# JSON-Based DAG Workflow Definition Schema

*A Technical Specification and Usage Guide*

------------------------------------------------------------------------

## Overview

This schema defines a **Directed Acyclic Graph (DAG)** workflow as a single JSON object. The workflow consists of **tasks** (graph nodes) connected by **dependencies** (edges). The JSON document specifies **global workflow configuration**, **task-level settings**, **error-handling strategies**, and optional **manual approvals**.

------------------------------------------------------------------------

## Top-Level Structure

A workflow definition is a single JSON object with three main parts:

```jsonc
{
  "Name": "...",          // required
  "Description": "...",   // optional
  "Configuration": { ... }, // optional global settings
  "Tasks": [ ... ]        // required list of task objects
}
```

### Fields

| Field           | Type   | Required | Default | Description                                                    |
| --------------- | ------ | -------- | ------- | -------------------------------------------------------------- |
| `Name`          | string | ✅        | —       | Unique name of the workflow.                                   |
| `Description`   | string | ❌        | null    | Human-readable description of the workflow’s purpose.          |
| `Configuration` | object | ❌        | `{}`    | Global execution settings such as parallelism and error rules. |
| `Tasks`         | array  | ✅        | `[]`    | Array of task objects defining the DAG.                        |

## Configuration Object

`Configuration` sets global execution rules.

```jsonc
"Configuration": {
  "DegreeOfParallelism": 3,
  "ErrorHandling": { ... },
  "Timeout": 600000
}
```

| Key                   | Type    | Required | Default | Meaning                                                          |
| --------------------- | ------- | -------- | ------- | ---------------------------------------------------------------- |
| `DegreeOfParallelism` | integer | ❌        | 3       | Maximum number of tasks running at the same time.                |
| `ErrorHandling`       | object  | ❌        | `{}`    | Default error-handling strategy for all tasks unless overridden. |
| `Timeout`             | integer | ❌        | null    | Maximum total runtime (milliseconds) for the entire workflow.    |

## ErrorHandling Object
Controls what happens when a task fails.

```jsonc
"ErrorHandling": {
  "Strategy": "Retry",
  "RetryPolicy": { ... }
}
```

| Key           | Type   | Required | Default | Description                            |
| ------------- | ------ | -------- | ------- | -------------------------------------- |
| `Strategy`    | enum   | ❌        | "Abort" | One of `"Retry"`, `"Skip"`, `"Abort"`. |
| `RetryPolicy` | object | ❌        | `{}`    | Used only if `Strategy` is `"Retry"`.  |

### RetryPolicy

```jsonc
"RetryPolicy": {
  "MaxRetries": 3,
  "BackoffStrategy": "Fixed",
  "InitialDelay": 1000,
  "MaxDelay": 10000,
  "BackoffCoefficient": 2.0
}
```

| Key                  | Type    | Default | Description                                                |
| -------------------- | ------- | ------- | ---------------------------------------------------------- |
| `MaxRetries`         | integer | 3       | Maximum retry attempts.                                    |
| `BackoffStrategy`    | enum    | "Fixed" | One of `"Fixed"`, `"Linear"`, `"Exponential"`, `"Jitter"`. |
| `InitialDelay`       | integer | 1000    | Delay (ms) before first retry.                             |
| `MaxDelay`           | integer | 10000   | Maximum allowed delay (ms) between retries.                |
| `BackoffCoefficient` | number  | 2.0     | Growth factor for linear or exponential backoff.           |


## Task Array
The `Tasks` array describes every node of the DAG.

Each task is an object:

```jsonc
{
  "Name": "ExtractData",
  "Description": "...",
  "Type": "Plugin.DataExtractor",
  "Parameters": { "source": "s3://bucket/raw" },
  "ErrorHandling": { ... },         // optional, overrides global
  "ManualApproval": { ... },        // optional human-in-the-loop
  "Timeout": 300000,
  "Dependencies": ["OtherTask"],
  "Output": "extractedData",
  "Position": { "X": 100, "Y": 50 }
}
```

| Key              | Type             | Required | Default     | Description                                                          |
| ---------------- | ---------------- | -------- | ----------- | -------------------------------------------------------------------- |
| `Name`           | string           | ✅        | —           | Unique identifier for the task.                                      |
| `Description`    | string           | ❌        | null        | Optional documentation.                                              |
| `Type`           | string / object  | ❌        | null        | Identifier or configuration describing how the task runs.            |
| `Parameters`     | object           | ❌        | `{}`        | Arbitrary key-value pairs passed to the task executor.               |
| `ErrorHandling`  | object           | ❌        | null        | Task-specific error handling, same format as global `ErrorHandling`. |
| `ManualApproval` | object           | ❌        | null        | Details for manual human approval (see below).                       |
| `Timeout`        | integer (ms)     | ❌        | null        | Maximum runtime for this task.                                       |
| `Dependencies`   | array of strings | ❌        | `[]`        | Names of tasks that must finish before this one starts.              |
| `Output`         | string           | ❌        | ""          | Name of variable or key where this task’s output will be stored.     |
| `Position`       | object           | ❌        | `{X:0,Y:0}` | Visual coordinates for DAG rendering.                                |


### ManualApproval Object

Defines an optional human-in-the-loop step.

```jsonc
"ManualApproval": {
  "Enabled": true,
  "Approvers": ["user@example.com"],
  "Instructions": "Review the data",
  "DefaultAction": "abort"
}
```

| Key             | Type          | Default | Description                                                        |
| --------------- | ------------- | ------- | ------------------------------------------------------------------ |
| `Enabled`       | boolean       | false   | If true, the task waits for human approval before continuing.      |
| `Approvers`     | array<string> | \[]     | Authorized users or groups who can approve.                        |
| `Instructions`  | string        | ""      | Guidance for approvers.                                            |
| `DefaultAction` | string        | "abort" | Action if approval is not granted in time (`"abort"` or `"skip"`). |

### Position Object

Used purely for visualization.

```jsonc
"Position": { "X": 100, "Y": 50 }
```

| Key | Type   | Description                           |
| --- | ------ | ------------------------------------- |
| X   | number | Horizontal coordinate (finite number) |
| Y   | number | Vertical coordinate (finite number)   |

------------------------------------------------------------------------

## Execution Semantics

1.  **DAG Dependency**: Each task runs only when every name listed in its `Dependencies` array has successfully completed (or been skipped based on error strategy).
2.  **Parallelism**: At most `DegreeOfParallelism` tasks run simultaneously, provided their dependencies are satisfied.
3.  **Error Handling**:
    -   A task's own `ErrorHandling` (if present) overrides the global setting.
    -   The engine applies retry logic when `Strategy` is `"Retry"`.
4.  **Manual Approval**: When `ManualApproval.Enabled` is true, execution pauses until an approver responds or a timeout triggers the `DefaultAction`.

------------------------------------------------------------------------

## Example JSON Workflow

```json
{
  "Name": "DataProcessingWorkflow",
  "Description": "ETL pipeline with validation and approval",
  "Configuration": {
    "DegreeOfParallelism": 4,
    "Timeout": 600000,
    "ErrorHandling": {
      "Strategy": "Retry",
      "RetryPolicy": {
        "MaxRetries": 5,
        "BackoffStrategy": "Exponential",
        "InitialDelay": 2000,
        "MaxDelay": 30000,
        "BackoffCoefficient": 2.5
      }
    }
  },
  "Tasks": [
    {
      "Name": "ExtractData",
      "Type": "Plugin.DataExtractor",
      "Parameters": { "source": "s3://bucket/raw" },
      "Output": "extractedData",
      "Position": { "X": 100, "Y": 50 }
    },
    {
      "Name": "ValidateData",
      "Type": "Plugin.DataValidator",
      "Dependencies": ["ExtractData"],
      "Parameters": { "schema": "customer-schema.json" },
      "ManualApproval": {
        "Enabled": true,
        "Approvers": ["data.lead@example.com"],
        "Instructions": "Review validation report before proceeding",
        "DefaultAction": "abort"
      },
      "Position": { "X": 300, "Y": 50 }
    },
    {
      "Name": "LoadData",
      "Type": "Plugin.DataLoader",
      "Dependencies": ["ValidateData"],
      "Parameters": { "destination": "postgres://etl-db/customers" },
      "Position": { "X": 500, "Y": 50 }
    }
  ]
}
```

------------------------------------------------------------------------

## Best Practices

-   **Validate** against a JSON Schema to ensure correct data types and required fields.
-   **Version Control**: Add external metadata (e.g., `"SchemaVersion"`) to handle future schema changes.
-   **Security**: Ensure `Approvers` map to real authenticated users or roles.
-   **Separation of Logic**: Keep `Type` and `Parameters` abstract enough to allow multiple execution engines.

------------------------------------------------------------------------

This JSON specification provides a **self-contained, engine-agnostic** format for describing DAG workflows, enabling interoperability across tools, editors, and execution platforms.