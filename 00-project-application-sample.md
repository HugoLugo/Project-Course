---
name: Hugo Lugo
neptun: ADFUXK
id: 2026-LG-02
---
# Visual ETL Application

## My interpretation of the brief
The project is about making data more accessible and understandable through a local-first visual ETL application. Users build data-processing workflows as a connected graph of nodes: sources, transformations, and outputs are represented as nodes connected by directed edges. The application reads data locally from CSV, JSON, XML, and APIs, processes it entirely on the user's device, and executes the resulting directed acyclic graph (DAG). The visual editor is a drag-and-drop canvas where users compose the workflow, connect nodes, and preview intermediate results at every step. The goal is to let users clean, combine, transform, and export data without writing code, while keeping source data private and local.

## Why I am a good fit for this project
I am motivated by the idea of making complex data operations accessible to people who do not have deep technical expertise. I am also strongly interested in data management and data pipelines. I want to deepen my understanding of how data flows through a system, how schemas and types are enforced, and how large datasets can be processed efficiently without overwhelming memory or the user. Finally, I am a good fit because I enjoy both the interface side and the logic side of software. I want to design a visual editor that feels intuitive, while also implementing the underlying executor and validation rules correctly.

## Relevant experience and background
Through my coursework I have built a foundation in programming, data handling, and web development. I understand basic graph algorithms, including topological sorting, which is directly relevant to executing a directed acyclic graph of processing nodes. I have also worked with data transformation concepts such as filtering, mapping, joining, and aggregation, and I am comfortable reasoning about data types and schemas. Above all, I am motivated to learn the parts I do not yet know. I see this project as a chance to deepen my skills in strict TypeScript, Svelte 5 and SvelteKit, DAG execution, schema and type compatibility, sandboxing, and large-file processing.

## Proposed approach
I would start by thinking about what the user actually needs: they have a file, they want to understand and clean it, and they shouldn't need to write code or upload anything to do that. So the experience I'm aiming for is simple, load the data, connect a few steps visually, and see what happens at each point. Before building anything, I'd want to know what that feels like from the user's side, because that shapes every decision after.

From there, I'd focus on the parts that are hardest to get right: how the workflow actually runs, and how the pieces talk to each other. figuring out the type system, how to handle custom code safely, and how to deal with bigger files is part of the work. I'd start with the core, test it early, and let the design grow from what I learn

## Initial plan
1. Define what the user experience should be and what the main flow looks like.
2. Explore the core: how the workflow runs and how the pieces connect.
3. Define the data types and when a connection between two steps is valid.
4. Build the required nodes and make sure each result can be inspected.
5. Build the visual editor and test how intuitive it feels.
6. Investigate how to run custom code safely and how to handle larger files.
7. Add saving, export, and tests for the most important cases.
8. Write up what I learned and where the limits are.
