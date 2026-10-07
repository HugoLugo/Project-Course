---
name: [Hugo Alberto Lugo Esperilla]
neptun: [ADFUXK]
id: [2026-LG-02]
github: [https://github.com/HugoLugo/Project-Course]
github_project: [https://github.com/HugoLugo/Project-Course/tree/main/00-project]
---

# Visual ETL Application

The Visual ETL Application is a node-based data preparation tool designed for users who need to clean, transform, combine, inspect, and export structured data without writing a full data-processing program. Users build workflows by connecting visual nodes for data sources, transformations, custom processing, and export. The application supports local CSV, JSON, and XML files as well as JSON API sources, provides previews of intermediate results, validates incompatible connections and configurations, and executes workflows according to their dependencies. By the end of the project, a user should be able to load data, build and run a complete transformation workflow, inspect the results, and export the processed output.

## Objectives
- **Primary objective:** Deliver a usable visual ETL application that allows non-programmer users to construct and execute data-processing workflows through a node-based interface.
- **Target users / stakeholders:** Users with basic familiarity with tabular data, such as spreadsheet users or small-business users, who do not necessarily have programming experience.
- **Measurable success criteria:** A user can load a supported data source, create and run a valid workflow containing transformations, preview intermediate results, handle understandable warnings or errors, and export the final result. Representative workflows should process datasets of at least 50,000 rows without crashing under normal test conditions.
- **Constraints:** Strict TypeScript; directed acyclic workflow graphs; local-first processing for local files; CSV, JSON, XML, and JSON API sources; isolated execution for Custom Code; no credentials, secrets, personal data, or production data committed to the repository.

## Scope
### In scope
- Creating visual workflows by adding and connecting compatible processing nodes.
- Loading data from CSV, JSON, XML, and JSON API sources.
- Core transformations including Filter, Select / Rename Fields, Aggregate, and Combine Tables.
- Previewing source and intermediate node results with clear validation, warning, and error feedback.
- Running workflows according to dependency order, including multi-input nodes and independent branches.
- Executing supported Custom Code in an isolated sandbox.
- Exporting table-like results as CSV and supported results as JSON.

### Out of scope
- User accounts, advanced authentication, or multi-user collaboration.
- Cloud-based processing of local source files.
- Workflow sharing, versioning, reusable templates, or incremental-result caching in the required version.
- Advanced transformations such as multiple Group by fields or complex multi-condition filters in the required version.

## Notes
The first implementation will focus on a reliable and understandable core workflow: load data, connect and configure transformations, run the workflow, inspect intermediate and final results, and export the output. The user interface should prefer simple terminology and clear explanations over ETL-specific technical language. More advanced features such as richer filtering, workflow templates, saved or shared workflows, and incremental execution may be considered only after the required workflow execution, validation, performance, and sandboxing behavior are reliable.