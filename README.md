# AI Swarm Team

AI Swarm Team is a small project that explores how multiple AI agents can work together to build software. Instead of asking one AI model to handle the entire development process, the project divides the work into separate stages and gives each stage its own agent.

The current workflow includes planning the application, generating source code, checking the generated files, and attempting to fix errors when something goes wrong.

The project uses locally running language models through Ollama, so you can experiment with the agent workflow without depending on a paid cloud AI API.

## How It Works

The workflow is managed through a shared state object called the **Swarm Blackboard**.

1. **Planner Agent** – Reads the user's requirement and prepares a high-level technical plan.
2. **Coder Agent** – Uses the plan to generate application files.
3. **Testing stage** – Writes the generated files into a folder and attempts to install their dependencies.
4. **Fixer Agent** – Attempts to correct errors reported during the workflow.
5. **Completion** – Returns the final status, generated files, and execution logs.

The process can repeat when an error needs to be fixed, subject to a maximum of six workflow iterations.

## Tech Stack

- TypeScript
- Node.js
- Express.js
- LangChain
- Ollama
- Qwen 2.5 Coder 3B
- Qwen3 4B
- Server-Sent Events (SSE)
- Zod
- dotenv

## Project Structure

```text
ai-swarm-team/
├── src/
│   ├── agents/
│   │   ├── PlannerAgent.ts
│   │   ├── CoderAgent.ts
│   │   └── FixerAgent.ts
│   ├── core/
│   │   └── SwarmBlackboard.ts
│   ├── utils/
│   │   └── llm.ts
│   ├── index.ts
│   └── server.ts
├── package.json
├── package-lock.json
├── tsconfig.json
└── .gitignore
```

## Getting Started

### Prerequisites

Before running the project, make sure you have:

- [Node.js](https://nodejs.org/) and npm installed.
- [Ollama](https://ollama.com/) installed and running.
- Enough available RAM and disk space to run the selected language models locally.

### 1. Clone the repository

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd ai-swarm-team
```

Replace the repository URL with your own GitHub URL.

### 2. Install dependencies

```bash
npm install
```

### 3. Download the required AI models

Open a terminal and run:

```bash
ollama pull qwen2.5-coder:3b
ollama pull qwen3:4b
```

These are the model names configured in `src/utils/llm.ts`.

Make sure Ollama is running and the models are available before starting the swarm.

### 4. Run the API server

```bash
npm run server
```

By default, the Express server starts at:

```text
http://localhost:4000
```

### 5. Run the swarm directly

To execute the sample requirement defined in `src/index.ts` without starting the API server, open another terminal and run:

```bash
npm start
```

The generated application files are written to the `generated-app` directory in the current working directory.

## API Endpoints

The server exposes the following endpoints.

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/swarm/run` | Starts a new swarm run. |
| GET | `/api/swarm/stream/:runId` | Streams workflow events using SSE. |
| GET | `/api/swarm/result/:runId` | Returns the run's status, files, and logs. |
| GET | `/api/runs` | Lists the runs held in server memory. |

### Example request

Send a POST request to `http://localhost:4000/api/swarm/run` with a JSON body:

```json
{
  "requirement": "Build a REST API for managing tasks with create, read, update and delete operations."
}
```

A successful request returns a run ID:

```json
{
  "runId": "run-...",
  "message": "Swarm started"
}
```

Use the returned ID to access the event stream and retrieve the result.

## Generated Output

Generated files are saved in the `generated-app` directory. The system attempts to install the generated application's dependencies before marking that stage successful.

**Note:** A successful dependency installation does not necessarily mean the generated application is bug-free or that all its features work correctly. Automated execution and functional testing of the generated application are not yet part of the current validation process.

## Current Limitations

- The language models must be available locally through Ollama.
- Run information is stored in server memory and is not persistent across server restarts.
- The workflow uses a fixed sequence of agent stages.
- Dependency installation is attempted, but it does not verify every generated feature.
- The API currently allows cross-origin requests through CORS; production deployment should use an appropriate origin policy and additional security controls.
- The generated application is not automatically deployed to a hosting service.

## Future Improvements

- Add automated tests for generated applications.
- Improve error detection and recovery between agents.
- Add persistent storage for projects and run history.
- Support more programming languages and project types.
-
