# Visual ETL Application

## 1. Actors and permissions

| Role | Needs | Responsibility / access |
| --- | --- | --- |
| User | Load, clean, transform, combine, inspect, and export data without requiring programming knowledge | Creates workflows, configures nodes, connects data-processing steps, runs workflows, previews intermediate results, handles warnings or errors, and exports results |

The primary target user is a person with basic familiarity with tabular data, such as a casual spreadsheet user or small-business owner, but without programming expertise.

The Custom Code node is considered an advanced feature for users who are comfortable writing JavaScript or TypeScript, but it does not introduce a separate permission role.

---

## 2. Use cases / user stories

### Create a workflow

- **Actor:** User
- **Precondition:** The application is open.
- **Main flow:**
  1. The system displays an empty workflow canvas and the available node types.
  2. The user drags a source node onto the canvas.
  3. The user adds transformation and output nodes.
  4. The user connects compatible output and input ports.
  5. The system displays the resulting directed workflow graph.
  6. Compatible connections remain active and available for execution.
- **Alternative / error flows:**
  - If the user attempts an incompatible connection, the system prevents the connection or visibly marks it as invalid and explains the reason in plain language.
  - If a connection would create a cycle, the system rejects it and explains that workflow loops are not allowed.
- **Postcondition:** A workflow graph exists on the canvas and can be configured, validated, and executed.

### Load a data source

- **Actor:** User
- **Precondition:** A CSV, JSON, XML, or API source node exists in the workflow.
- **Main flow:**
  1. The user selects or configures the source.
  2. For file sources, the application reads the selected local file. For API sources, the application performs the configured GET request.
  3. The system reads an initial sample of the data.
  4. The system determines the transferable data shape.
  5. For structured data, the system infers an initial schema.
  6. The system displays detected fields and types where applicable.
  7. The system displays a limited preview of the source data.
  8. The source becomes available to downstream nodes.
- **Alternative / error flows:**
  - Invalid CSV, JSON, or XML data produces a clear parsing error.
  - A failed API request produces a clear network or HTTP error.
  - Ambiguous field types are treated as Text.
  - Dirty or inconsistent values are retained where possible and may later produce warnings in transformations.
- **Postcondition:** The source produces one of the supported data shapes and provides its schema information to downstream nodes.

### Transform data

- **Actor:** User
- **Precondition:** A compatible upstream node provides input data.
- **Main flow:**
  1. The user adds a transformation node.
  2. The user connects the node to a compatible upstream output.
  3. The user selects the transformation node.
  4. The system displays the node configuration in the right-side configuration panel.
  5. The user configures the transformation.
  6. The system validates the configuration.
  7. When the workflow runs, the node processes the provided input.
  8. The node produces an output that can be previewed or used as another node's input.
- **Alternative / error flows:**
  - Missing required configuration causes the node to become invalid.
  - If a referenced field is removed or renamed upstream, the node becomes invalid and explains which field is unavailable.
  - If individual rows contain values that cannot be processed but a meaningful result can still be produced, the node completes with a warning.
- **Postcondition:** The transformed output is available for preview and downstream processing.

### Combine two datasets

- **Actor:** User
- **Precondition:** Two compatible table-like outputs are available.
- **Main flow:**
  1. The user adds a Combine Tables node.
  2. The user connects two table inputs.
  3. The user selects one matching field from the first table.
  4. The user selects one matching field from the second table.
  5. The user selects whether to keep only matching rows or all rows from the first table.
  6. The system validates the selected fields and their types.
  7. During execution, the node waits until both required inputs have completed successfully.
  8. The system performs the key-based join.
  9. The combined table becomes available for preview and downstream processing.
- **Alternative / error flows:**
  - If either required input is missing, the node cannot execute.
  - If an input is not table-like data, the connection is invalid.
  - If the selected key fields have incompatible types, the node cannot execute.
  - Duplicate keys are allowed and may produce multiple matching rows.
- **Postcondition:** A combined table is produced.

### Preview a node result

- **Actor:** User
- **Precondition:** The selected node has produced a result.
- **Main flow:**
  1. The user selects a node.
  2. The system displays the node output in the preview area.
  3. For large collections or tables, the system displays only a limited sample, such as the first 50–100 rows or items.
  4. The system displays relevant warning information when applicable.
  5. The node output remains available for downstream connections.
- **Alternative / error flows:**
  - If the node has not executed yet, the system indicates that no result is available.
  - If the node failed, the system displays the error instead of an output preview.
- **Postcondition:** The user can inspect the intermediate result without interrupting or consuming the workflow output.

### Run the workflow

- **Actor:** User
- **Precondition:** A workflow exists on the canvas.
- **Main flow:**
  1. The user selects Run workflow.
  2. The system validates the graph, node configurations, required inputs, type compatibility, and cycles.
  3. If valid, the system determines a topological execution order.
  4. Nodes begin executing when all required dependencies are satisfied.
  5. Independent branches may execute separately or concurrently.
  6. Multi-input nodes wait for every required input.
  7. The interface displays each node's execution state.
  8. Successfully completed outputs become available to dependent nodes.
  9. Execution finishes when all runnable nodes have reached a final state.
- **Alternative / error flows:**
  - If validation fails, execution does not begin and the user receives clear explanations.
  - If a cycle is detected, execution does not begin.
  - If a node fails during execution, nodes depending on its result do not execute.
  - Independent branches that do not depend on the failed node may continue.
  - Warnings do not stop downstream execution.
  - The user may cancel an active execution.
- **Postcondition:** The workflow completes, is cancelled, or ends with clearly visible execution results and errors.

### Export a result

- **Actor:** User
- **Precondition:** An Export node is connected to a compatible upstream output.
- **Main flow:**
  1. The user configures the export format.
  2. The user chooses the output filename and local save location.
  3. The user runs the workflow.
  4. The Export node waits for the upstream node to complete successfully.
  5. The Export node receives the resulting data.
  6. The system writes the data to the selected local location.
  7. The system confirms successful export.
- **Alternative / error flows:**
  - An unsupported combination of data shape and export format produces a clear error.
  - If the output file cannot be written, the Export node fails and explains the reason.
- **Postcondition:** The processed result is stored as a local file.

### Handle warnings and errors

- **Actor:** User
- **Precondition:** A node or connection contains a warning, invalid state, or error.
- **Main flow:**
  1. The system visually marks the affected node or connection.
  2. The user selects the affected element.
  3. The system explains the issue using plain-language terminology.
  4. Where possible, the system identifies the affected field, input, or number of problematic rows.
  5. The user changes the source, configuration, or connection.
  6. The system revalidates the affected workflow elements.
- **Alternative / error flows:**
  - Where a problem does not prevent meaningful processing, it is represented as a warning rather than an error.
  - Optional contextual examples or visual help may be displayed for selected errors if implemented.
- **Postcondition:** The user can understand the problem and attempt to correct it without relying on technical error codes.

### Execute custom code

- **Actor:** User
- **Precondition:** A Custom Code node has at least one valid upstream input.
- **Main flow:**
  1. The user adds a Custom Code node.
  2. The user connects one or more supported inputs.
  3. The user writes supported JavaScript or TypeScript code.
  4. The system passes only the explicitly connected input values into an isolated sandbox.
  5. The sandbox executes the custom transformation.
  6. The code returns one supported transferable data shape.
  7. The system validates the returned value.
  8. The result becomes available for preview and downstream processing.
- **Alternative / error flows:**
  - A runtime exception causes only the Custom Code node to fail.
  - Unsupported return values cause the node to fail with a clear error.
  - Execution exceeding the configured time or resource boundary is terminated.
  - The custom code is not permitted direct filesystem or network access.
- **Postcondition:** The node either produces a supported result or fails without compromising the rest of the application.

---

## 3. Functional requirements

| Requirement | Acceptance criteria | Priority |
| --- | --- | --- |
| The user can create a workflow by dragging nodes onto a visual canvas. | Given an empty workflow, when the user drags a node onto the canvas, then the node appears and can be configured. | Must |
| The user can create directed connections between compatible nodes. | Given two compatible ports, when the user connects them, then the directed connection is stored and displayed. | Must |
| The system prevents or marks incompatible connections. | Given incompatible output and input shapes, when the user attempts or retains the connection, then the system clearly marks it as invalid and explains why. | Must |
| The system prevents cycles. | Given a connection that would create a cycle, when the user attempts the connection, then the system rejects it and explains the problem. | Must |
| The application supports local CSV sources. | Given a valid CSV file, when selected, then the application parses it, infers a schema, and displays a preview. | Must |
| The application supports local JSON sources. | Given valid JSON containing a supported transferable shape, when selected, then the application detects the shape and displays a preview. | Must |
| The application supports local XML sources. | Given valid XML using a supported common structure, when selected, then the application converts it into the internal data representation and displays a preview. | Must |
| The application supports API sources. | Given a valid GET endpoint returning JSON, when the workflow runs, then the application retrieves the response and converts it into a supported internal data shape. | Must |
| The application detects a source schema from an initial sample. | Given structured source data, when the source is loaded, then fields are assigned inferred Text, Number, Boolean, or missing-value characteristics. | Must |
| Ambiguous inferred values default to Text. | Given a sampled field whose type cannot be safely identified, when schema inference completes, then the field is treated as Text. | Must |
| Dates are initially treated as Text. | Given date-like source values, when schema inference runs, then the values are not automatically assigned a separate Date type. | Must |
| The Filter node can filter table rows using one condition. | Given table-like data, when the user configures a valid field, condition, and value, then only rows satisfying that condition are returned. | Must |
| Filter conditions depend on the selected field type. | Given a numeric field, the system provides numeric comparison options; given a text field, the system provides appropriate text conditions. | Must |
| Filter conditions use plain-language labels. | Given a numeric comparison, the interface displays labels such as “greater than” rather than requiring mathematical comparison symbols. | Must |
| Invalid or missing row values do not automatically stop filtering. | Given some incompatible values, when valid rows can still be processed, then the Filter node returns the valid result and displays a warning describing skipped rows. | Must |
| The Select / Rename Fields node allows fields to be selected or removed. | Given a table, when the user deselects fields, then those fields are removed from the output schema. | Must |
| The Select / Rename Fields node allows selected fields to be renamed. | Given a selected field, when the user provides a unique new name, then downstream nodes see the new field name. | Must |
| Field renaming preserves the field data type. | Given a numeric field, when renamed, then it remains numeric in the output schema. | Must |
| Duplicate output field names are prevented. | Given two fields configured with the same output name, when validation runs, then the node is marked invalid until unique names are provided. | Must |
| At least one field must remain selected. | Given all fields deselected, when validation runs, then the node is marked invalid. | Must |
| The Aggregate node supports Sum, Average, and Count. | Given compatible table data, when the user selects one of the supported operations, then the node calculates the requested aggregation. | Must |
| The Aggregate node supports one optional Group by field. | Given a Group by field, when aggregation executes, then one aggregated result is produced per distinct group. | Must |
| Ungrouped aggregation produces a single value. | Given no Group by field, when Sum, Average, or Count executes, then the output shape is a single value. | Must |
| Grouped aggregation produces table-like output. | Given a Group by field, when aggregation executes, then the output contains grouped records and calculated values. | Must |
| The Combine Tables node accepts two table-like inputs. | Given two connected table outputs, when both complete successfully, then the node can execute. | Must |
| The Combine Tables node supports key fields with different names. | Given compatible fields with different names, when selected as matching keys, then the join is allowed. | Must |
| The Combine Tables node supports matching-only joins. | Given two datasets, when the user selects “only rows with a match,” then only matching key combinations appear in the result. | Must |
| The Combine Tables node supports keeping all rows from the first table. | Given two datasets, when the user selects “all rows from the first table,” then unmatched first-table rows remain in the output. | Must |
| Duplicate join keys are supported. | Given repeated matching keys, when the join executes, then all valid matching row combinations are produced. | Must |
| Key fields must have compatible types. | Given selected key fields with incompatible inferred types, when validation runs, then the node is marked invalid. | Must |
| Every node output can be used as another compatible node input. | Given a completed node output, when a compatible downstream connection exists, then the output is provided to that downstream node. | Must |
| Every completed node output can be previewed. | Given a completed node, when selected, then the system displays its result or a limited sample. | Must |
| Large previews are limited to a sample. | Given a large table or list, when previewed, then the application displays only a bounded number of rows/items rather than rendering the entire dataset. | Must |
| The application supports four transferable data shapes. | Given valid workflow data, then the system can represent a single value, value list, object, and table/object array. | Must |
| Port compatibility is validated using data shape. | Given an output shape and a target input requirement, then the connection is accepted only when the shapes are compatible. | Must |
| Field-level compatibility is checked where required. | Given operations such as joins or numeric aggregation, then relevant field types are validated in addition to port shape. | Must |
| Invalid existing connections remain visible. | Given a previously valid connection that becomes incompatible after configuration changes, then the system keeps it visible, marks it invalid, and explains the reason. | Must |
| The system distinguishes warnings from errors. | Given a recoverable data problem, the node completes with a warning; given a problem that prevents a valid result, the node enters an error state. | Must |
| The system validates the full workflow before execution. | Given a workflow, when Run workflow is selected, then cycles, required inputs, node configuration, and compatibility are checked before execution starts. | Must |
| The executor uses topological dependency ordering. | Given a valid DAG, when executed, then a node runs only after all required upstream dependencies are satisfied. | Must |
| Multi-input nodes wait for all required inputs. | Given a Combine Tables or multi-input Custom Code node, when only one input is available, then the node remains waiting. | Must |
| Independent branches can continue after an unrelated failure. | Given one failed branch and one independent valid branch, when execution continues, then the independent branch may still complete. | Must |
| A failed node blocks dependent nodes. | Given a node failure, when downstream nodes depend on its result, then those dependent nodes do not execute. | Must |
| Every Run workflow action begins a fresh execution. | Given a previously completed workflow, when Run workflow is selected again, then the workflow is executed again from a fresh execution state. | Must |
| The user can cancel active execution. | Given an active workflow execution, when the user selects Cancel, then active processing is stopped safely. | Must |
| Node execution states are shown visually. | During execution, nodes display states such as Waiting, Running, Completed, Warning, Error, or Invalid. | Must |
| The Custom Code node executes only in an isolated sandbox. | Given custom code, when executed, then it cannot run directly in the main application environment. | Must |
| Custom Code cannot directly access the filesystem. | Given sandboxed code, attempts to directly access local files outside provided input fail. | Must |
| Custom Code cannot directly access the network. | Given sandboxed code, direct network requests are unavailable. | Must |
| Custom Code execution is bounded. | Given non-terminating or excessively long code, when the configured limit is exceeded, then execution is terminated. | Must |
| Custom Code returns only supported transferable data shapes. | Given a returned value, when validation runs, then unsupported outputs are rejected with a clear message. | Must |
| Table-like results can be exported as CSV. | Given table output connected to Export and CSV selected, when execution reaches the node, then a local CSV file is written. | Must |
| Any supported transferable shape can be exported as JSON. | Given a supported output and JSON selected, when execution reaches the Export node, then a local JSON file is written. | Must |
| Export uses the normal workflow execution mechanism. | Given a configured Export node, when Run workflow is selected, then export occurs only after upstream processing completes. | Must |
| Local source data remains on the user's device during processing. | Given local files, when the workflow executes, then their contents are not uploaded to an external processing service. | Must |
| The application handles representative datasets of at least 50,000 rows without crashing. | Given representative 50,000-row test datasets and supported workflows, when executed under normal test conditions, then the application completes or fails gracefully without crashing. | Must |
| The application should handle representative datasets of up to 100,000 rows where the workflow and memory strategy permit it. | Given suitable 100,000-row datasets, when supported transformations execute, then the application completes without crashing under normal test conditions. | Should |
| The application may support multiple conditions with AND / OR inside one Filter node. | Given multiple configured conditions, the Filter node evaluates the selected logical relationship. | Could |
| The application may support Starts with and Ends with text filters. | Given a text field, the user may select additional text comparison options. | Could |
| The application may support multiple Group by fields. | Given multiple selected grouping fields, aggregation may produce grouped combinations. | Could |
| The application may display richer visual examples for selected errors. | Given a supported error, the interface may show a small example explaining the expected and actual data shapes. | Could |
| The user may start from reusable workflow templates. | Given available templates, the user may create a workflow from a predefined graph configuration. | Could |
| Selected fields may be manually reordered. | Given selected fields, the user may change the output column order. | Could |
| Workflow results may be cached for incremental execution. | Given an unchanged branch, a later implementation may reuse previous results instead of executing every node again. | Could |
| Workflows may be saved, versioned, or shared without source data. | Given a workflow configuration, a later implementation may persist and exchange the graph separately from source data. | Could |

Priority: **Must** = required for the final product; **Should** = important and targeted where practical; **Could** = optional extension if time permits.

---

## 4. Business rules and constraints

| Rule or constraint | Rationale |
| --- | --- |
| A workflow must form a directed acyclic graph. | Cycles prevent deterministic dependency-based execution. |
| Every node may execute only after all required upstream inputs are successfully available. | Ensures correct dependency handling. |
| A multi-input node must wait for every required input. | Prevents incomplete joins or custom transformations. |
| Every node output may become an input to another compatible node. | Enables reusable and composable processing pipelines. |
| Connections must satisfy transferable data-shape compatibility. | Prevents invalid operations before execution. |
| Some nodes require additional field-level type compatibility. | A compatible table shape alone is insufficient for operations such as key-based joins or numeric aggregation. |
| The supported transferable shapes are single value, value list, object, and table/object array. | Defines the common data contract between nodes. |
| User-facing terminology should prefer simple terms such as Table, List, and Single value over internal terms such as object array or scalar. | Supports non-programmer users. |
| Dates are treated as Text in the initial implementation. | Avoids unreliable automatic date inference in the MVP. |
| Ambiguous schema values default to Text. | Reduces unsafe automatic conversions. |
| Automatic silent conversion between incompatible data shapes is not performed. | Keeps workflow behavior predictable and understandable. |
| A warning indicates that a meaningful result was still produced. | Separates recoverable data-quality issues from execution failure. |
| An error indicates that a valid result cannot be produced. | Gives consistent failure semantics. |
| Invalid existing connections remain visible rather than being automatically deleted. | Helps the user understand what changed and how to repair the workflow. |
| A Filter node contains one condition in the required implementation. | Keeps the beginner-facing UI simple while allowing multiple Filter nodes to represent logical AND behavior. |
| Filter conditions depend on the selected field type. | Prevents invalid operations such as text containment on numeric fields. |
| Missing or invalid row values may be skipped with warnings when a meaningful transformation result remains possible. | Dirty source data should not unnecessarily stop the full workflow. |
| A Select / Rename Fields node must keep at least one field. | Prevents meaningless empty table schemas. |
| Output field names within a structured result must be unique. | Prevents ambiguous downstream field references. |
| An ungrouped Aggregate produces a single value. | Provides a predictable data-shape rule. |
| A grouped Aggregate produces table-like data. | The group key and calculated result must both be represented. |
| The required Aggregate operations are Sum, Average, and Count. | Matches the required project scope. |
| The required Combine Tables node uses exactly two inputs in the MVP. | Keeps multi-input joining manageable while satisfying the required key-based join behavior. |
| Combine Tables supports matching-only and keep-all-first-table behavior. | Provides common inner-join and left-join functionality without exposing unnecessary complexity. |
| Join key fields must have compatible types. | Prevents unpredictable matching. |
| The Custom Code node is an advanced feature and is not required for normal non-programming workflows. | Preserves accessibility for the primary target user. |
| Custom code must not directly access the local filesystem or network. | Limits security exposure from user-provided code. |
| Local file contents must not be uploaded to an external processing service. | Preserves the local-first privacy model. |
| API Source is limited to GET requests with JSON responses in the required implementation. | Keeps the API node focused on data input rather than general HTTP-client behavior. |
| CSV export is limited to table-like output. | Avoids forcing non-tabular data into an artificial CSV structure. |
| JSON export supports all transferable data shapes. | JSON naturally represents all required internal data shapes. |
| Every workflow run starts from a fresh execution state. | Simplifies correctness, testing, and failure behavior for the MVP. |
| Saving, versioning, sharing, templates, and advanced authentication are outside the required MVP. | Prevents optional extensions from expanding the required thesis scope. |

---

## 5. User interface and workflow

The main application consists of a visual node-based workflow editor designed for users who understand basic data concepts but are not expected to know programming or ETL terminology.

The main interface contains:

- a **node palette** on the left;
- a **drag-and-drop workflow canvas** in the center;
- a **configuration panel** on the right;
- a **data preview area** for selected node results;
- **Run workflow** and **Cancel** controls;
- visual node and connection states for validation and execution.

The node palette is organized into simple categories.

### Sources

- CSV File
- JSON File
- XML File
- API

### Transformations

- Filter
- Select / Rename Fields
- Aggregate
- Combine Tables
- Custom Code

### Output

- Export

Technical terminology is simplified in the user interface. For example:

- scalar → Single value;
- scalar array → List;
- object array → Table;
- key-based join → Combine Tables.

Data-shape information is displayed subtly on node ports or when the user selects or inspects a node.

When configuring a Filter node, the interface displays field-type-appropriate conditions using plain language. Numeric comparisons use labels such as “greater than” and “less than” rather than requiring symbols such as `>` or `<`.

Selecting a node opens its configuration in the right panel. Complex configuration forms are not displayed directly inside canvas nodes.

After successful execution, selecting a node displays a limited result preview. Table-like data is displayed similarly to a small spreadsheet. The preview does not consume or terminate the node output.

Invalid connections remain visible and are visually distinguished. Selecting an invalid connection displays a plain-language explanation such as:

“This connection cannot be used because Filter needs table data, but Aggregate currently produces a single value.”

During execution, nodes visually communicate their state:

- Waiting
- Running
- Completed
- Completed with warning
- Error
- Invalid configuration

A warning does not prevent downstream execution if a meaningful output exists. An error blocks nodes that depend on the failed result.

The expected basic user workflow is:

1. Add a source.
2. Load or configure the data.
3. Add and connect processing nodes.
4. Configure each node through the right panel.
5. Preview available source or intermediate results.
6. Select Run workflow.
7. Observe execution states and resolve warnings or errors if necessary.
8. Inspect results.
9. Export the final output.

---

## 6. Non-functional requirements

| Requirement | How it will be verified |
| --- | --- |
| The application must process representative datasets containing at least 50,000 rows without crashing under normal test conditions. | Performance test using representative CSV/JSON datasets and common Filter, Aggregate, Select/Rename, and Combine Tables workflows. |
| The application should process representative datasets containing up to 100,000 rows where the selected workflow and memory strategy permit it. | Extended performance test with larger datasets and documented memory/performance results. |
| Large datasets must not be fully rendered in the preview interface. | UI test confirming that previews are limited to a bounded sample such as the first 50–100 rows/items. |
| Long-running processing should not permanently freeze or crash the user interface. | Manual and automated testing during large-file workflows. |
| Active workflow execution must be cancellable. | Start a representative long-running workflow, trigger Cancel, and verify that active work stops safely. |
| A first-time user with basic spreadsheet familiarity should be able to load a CSV, add a Filter, preview the result, and export it without external documentation. | Observed usability test with representative users. |
| Error messages must describe the problem using plain-language terminology. | Usability review of representative invalid connections, missing fields, source parsing errors, and failed nodes. |
| Local file data must not be uploaded to an external processing service. | Architecture review and network inspection during representative local-file workflows. |
| API data may leave or enter the device only through the API Source request explicitly configured by the user. | Network inspection during API-source testing. |
| Invalid node configuration must not crash the application. | Integration tests using missing fields, invalid input shapes, incompatible join keys, and malformed configuration. |
| A failed node must not corrupt or stop an unrelated independent branch. | Integration test containing at least two independent workflow branches where one branch fails. |
| A new Run workflow operation must start from a clean execution state. | Run the same modified workflow repeatedly and verify that stale node execution states or outputs are not incorrectly reused. |
| Custom Code execution must be isolated from direct filesystem and network access. | Security tests against the selected sandbox implementation. |
| Custom Code must be terminated when its configured execution limit is exceeded. | Sandbox test using deliberately non-terminating or excessive code. |
| Strict TypeScript must be used in the application codebase. | Type-checking and build verification in the version-controlled project. |
| Secrets, tokens, personal data, and production data must not be committed to Git. | Repository review and secret-scanning process before submission. |

---

## 7. Open questions and risks

| Question / risk | Impact | Owner | Resolution / decision |
| --- | --- | --- | --- |
| Which sandbox technology should be used for Custom Code? | Security, compatibility, and implementation complexity | Student | Compare candidate isolation approaches and prototype the most promising option before final selection. |
| Should Custom Code support JavaScript only or both JavaScript and TypeScript? | User capability and sandbox complexity | Student | Keep open until the sandbox prototype demonstrates the practical cost of TypeScript transpilation/execution. |
| What exact execution timeout and resource limits should be used for Custom Code? | Security and usability | Student | Determine experimentally using representative transformations and malicious/non-terminating test cases. |
| How reliably will the selected visual graph library integrate with Svelte 5 / SvelteKit? | Core editor implementation | Student | Build an early prototype containing draggable nodes, directed connections, and connection validation. |
| How should XML structures be normalized into the four transferable data shapes? | Source compatibility | Student | Support common repeated-element structures first and document unsupported advanced XML features. |
| How accurate is schema inference on dirty or mixed source data? | Type validation and transformation behavior | Student | Use conservative inference, default ambiguous fields to Text, and test against representative dirty datasets. |
| How much memory will key-based joins require for large datasets? | Ability to meet the 50,000–100,000 row target | Student | Benchmark different join strategies and document memory limits. |
| Which transformations can operate in streaming/chunked mode? | Memory usage and performance | Student | Prototype streaming/chunking for sources, filtering, and aggregation; buffer only where required. |
| Will Tauri packaging change filesystem, security, or sandbox behavior compared with the development proof of concept? | Desktop deployment | Student | Validate representative file loading, export, API access, and custom-code isolation in the packaged application. |
| How should progress be represented for operations where total work cannot be predicted accurately? | User feedback during long operations | Student | Begin with execution-state feedback; add percentage progress only where a reliable total can be measured. |
| Can many-to-many join keys unexpectedly multiply the result size? | Performance and user understanding | Student | Allow duplicate keys as required; consider a warning for unexpectedly large result expansion as a later usability improvement. |