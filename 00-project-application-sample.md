---
name: Jane Doe
neptun: ABC123
id: 2026-LG-02
---
# Sokoban Web Game

## My interpretation of the brief
The project is a browser-based implementation of Sokoban, a grid-based puzzle game in which the player pushes crates onto target tiles. The application must provide responsive gameplay, a collection of levels, and persistent progress. The client is responsible for rendering and interaction, while the backend stores level data and completed-level progress.

## Why I am a good fit for this project
I am interested in interactive web applications and game development. Sokoban has a small, clear ruleset, but a complete implementation still requires careful state management, input handling, rendering, and persistence. This makes it a good opportunity to demonstrate both client-side and backend development in a manageable scope.

## Relevant experience and background
I have worked with TypeScript, web interfaces, and relational databases in coursework and personal projects. I am comfortable using Git and breaking a larger task into small, testable features. I have also experimented with canvas-based rendering, which gives me a useful starting point for working with PixiJS.

## Proposed approach
I would first implement the Sokoban rules in isolation and verify movement, collision, crate pushing, and level-completion logic with automated tests. I would then build the PixiJS game client, followed by SvelteKit API routes and Prisma persistence for levels and player progress. The first complete version would let a player choose a level, solve it in the browser, and see the completed state stored by the backend.

## Initial plan
1. Define the game rules, player flow, and level-data format.
2. Write the specification and initial technical proposal.
3. Prototype core Sokoban movement and win-condition logic.
4. Build the PixiJS client and integrate it with the SvelteKit application.
5. Add Prisma persistence for levels and player progress.
6. Test gameplay and evaluate usability with a small set of players.

## Additional information
I plan to maintain a Git repository from the start and use issues to track work. The project will use TypeScript throughout, with PixiJS for rendering, SvelteKit for backend routes, and Prisma for database access.
