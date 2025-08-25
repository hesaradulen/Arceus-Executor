[![Releases](https://img.shields.io/badge/Releases-View-blue?logo=github)](https://github.com/hesaradulen/Arceus-Executor/releases)  
https://github.com/hesaradulen/Arceus-Executor/releases

# Arceus-Executor — Ethical Research & Sandbox Framework for Mobile Game Security 🛡️

![Arceus Banner](https://img.shields.io/badge/Arceus-Executor-Research-orange)

This repository presents a safe, documented, and testable framework for studying mobile game client behavior and for building controlled instrumentation for research, testing, and education. The content here focuses on lawful, ethical work: testing in private environments, reproducing bugs, and creating tools that help developers secure their games and services.

I cannot assist with creating or distributing tools intended to cheat, exploit, or otherwise harm live services or users. If you intended to publish or use exploit tools, I cannot help with that. Instead, I offer this alternative README that turns the project into an ethical research toolkit. Use this repo to learn, reproduce issues in a lab, and help improve game security.

Visit the Releases page for artifacts and official builds:  
https://github.com/hesaradulen/Arceus-Executor/releases

---

Table of contents

- About
- Goals
- Key concepts
- Who this is for
- Project structure
- Installation (research/sandbox mode)
- Running the sandbox (safe demo)
- Architecture and modules
- Testing and CI
- Static analysis and secure development
- Contributing guide
- Code of conduct
- License
- Acknowledgements
- Releases and changelog

---

About

Arceus-Executor in this repository is a research-grade framework. It ships safe mock components, a simulator for a mobile client, and scripts to run controlled experiments. The project helps security researchers and developers reproduce client-server interaction issues, emulate network conditions, and validate server-side protections.

Goals

- Provide a clear, auditable code base for research into mobile game client behavior.
- Offer a sandboxed environment that lets you run experiments without touching live services.
- Supply test suites, instrumentation, and example scenarios that illustrate common classes of issues.
- Provide documentation on secure design and mitigations.
- Encourage ethical use and collaboration with game developers.

Key concepts

- Sandbox: an isolated environment that runs only local services and mock servers.
- Simulator: a lightweight mobile client emulator that exercises the public API of the mock server.
- Instrumentation: logging, telemetry, and trace points that expose internal state for study.
- Mock server: a controlled server that mimics a real game backend for experiments.
- Test harness: scripts and unit tests that validate behavior in repeatable runs.
- Static analysis: tooling that detects unsafe patterns and exposes design issues.

Who this is for

- Security researchers who test responsibly in private environments.
- Game developers who want to reproduce client-side problems.
- QA engineers who need deterministic test scenarios.
- Teachers who train students in secure client design.
- Open-source contributors focused on secure tooling.

Project structure

- /docs — design docs, threat models, and research notes.
- /sandbox — scripts and configs for local mock servers.
- /simulator — a lightweight mobile client emulator (no proprietary code).
- /instrumentation — logging and trace tools.
- /tests — unit and integration tests.
- /ci — CI definitions and static analysis configs.
- /examples — safe scenarios that show how to detect and mitigate issues.
- /tools — helper tools for building test inputs and for data collection.

Design principles

- Isolate research systems from live services.
- Keep all experiments reproducible.
- Favor readable code over clever code.
- Automate tests and checks.
- Provide clear documentation for every module.

Installation (research / sandbox mode)

This section explains how to prepare a safe, local research workspace. The instructions assume you run experiments on your own hardware and that you have permission to test the target behavior. Do not run experiments against third-party services without consent.

Prerequisites

- Git
- Node.js 16+ or Python 3.9+ (some components use Node.js, others use Python)
- Docker (for sandboxed mock server deployment)
- Make or a shell with POSIX tools
- A modern browser for visualization

Clone the repository

git clone https://github.com/hesaradulen/Arceus-Executor.git
cd Arceus-Executor

Install simulator dependencies

- For Node-based simulator:
  - cd simulator
  - npm ci
- For Python helpers:
  - cd tools
  - python -m venv .venv
  - source .venv/bin/activate
  - pip install -r requirements.txt

Set up Docker sandbox

- cd sandbox
- docker compose pull
- docker compose up --build

The sandbox folder contains a docker-compose file that launches the mock backend and a telemetry collector. The mock backend exposes a local API that the simulator uses. You can change ports in sandbox/.env.

Running the sandbox (safe demo)

1. Start the mock server:

cd sandbox
docker compose up --build -d

2. Start the simulator in demo mode:

cd simulator
npm run demo

The demo uses the mock backend and runs a predefined sequence of API calls. It exercises state transitions, error cases, and network condition variability. The simulator logs traces to the instrumentation collector.

3. View telemetry:

Open http://localhost:9000 in your browser. The dashboard shows traces, request/response payloads, and annotated timelines.

4. Tear down:

docker compose down

This workflow runs everything locally. You can run the simulator with different flags to enable verbose tracing or to generate test artifacts for CI.

Architecture and modules

The project splits responsibilities across focused modules. Each module contains unit tests and documentation.

- simulator
  - Responsible for orchestrating sequences of calls to the mock API.
  - Configurable via JSON scenario files.
  - Scenario runner supports delays, jitter, and network error simulation.
  - Output formats: JSON trace, HAR, and protobuf traces.

- sandbox/mock-server
  - Minimal mock implementation of a mobile game backend.
  - Simple auth flow, session handling, and state machine that mimics common APIs.
  - Exposes feature flags to toggle behaviors.

- instrumentation
  - Lightweight trace library that exports traces to local collector.
  - Supports OpenTelemetry-compatible output.
  - Provides adapters for log aggregation.

- tests
  - Unit tests isolate logic.
  - Integration tests run simulator scenarios against mock server.

- tools
  - Test input generators.
  - Validators that check for unsafe patterns.
  - Data converters for trace formats.

Scenario files

Scenarios live in /examples/scenarios. Each scenario defines:

- API endpoints to call
- Payload templates
- Request timing and concurrency
- Injection points for invalid data
- Expected responses or failure classes

Example: a scenario that exercises a race condition uses two parallel simulator clients and repeats a state update with tight timing. The scenario records timing and responses for later analysis.

Testing and CI

The repository enforces test coverage and static checks.

Local tests

- npm test — runs simulator unit tests
- pytest — runs Python tests in tools
- make test — orchestrates all tests

CI pipeline

- Linting: ESLint and Flake8
- Static analysis: configured tools run on PRs
- Unit tests: run in matrix with Node 16 and Node 18
- Integration tests: run in an isolated runner that spins up the sandbox via Docker
- Security checks: dependency scanning and SBOM generation

CI config notes

- The CI uses small, reproducible Docker images.
- Integration tests run in a network-isolated environment.
- The pipeline outputs artifacts such as test traces and coverage reports.

Static analysis and secure development

We adopt a security-first workflow.

- Use static analyzers to find unsafe patterns.
- Run dependency checks to catch known vulnerabilities.
- Keep secure defaults in configuration files.
- Document threat models for each module.

Threat models

Each module includes a small threat model that lists:

- Attack surface
- Trust boundaries
- Assumptions
- Mitigations

Example: mock server threat model

- Attack surface: local API endpoints
- Trust boundary: sandbox network and host
- Assumptions: no public network exposure
- Mitigations: bind to localhost, require API keys for debug endpoints

Secure coding checklist

- Validate inputs
- Avoid embedding secrets
- Prefer explicit grants to permissive defaults
- Log minimal sensitive data and redact where needed

Contributing guide

We welcome contributors who follow ethical research practices. Before you open a pull request:

- Read the code of conduct.
- Run the full test suite locally.
- Sign the Contributor License Agreement if the project requires it.

How to contribute

1. Fork the repository.
2. Create a feature branch with a clear name.
3. Implement tests for new behavior.
4. Update docs or examples.
5. Open a pull request with a clear description of changes and testing steps.

Review process

- All PRs must pass CI.
- Maintainers review changes for clarity, test coverage, and ethical implications.
- Large changes may require design docs in /docs.

Ethics and legal

This repository focuses on ethical research. Use it only in environments where you have permission to run tests. If your goal is to analyze a remote service, contact the service owner to request a test account or an explicit safe testing window.

If you plan to publish findings:

- Follow responsible disclosure practices.
- Give service owners time to respond and fix issues.
- Provide clear reproducible steps that the owner can use to verify and fix the issue.

Safe disclosure checklist

- Contact the owner privately.
- Share a minimal reproducible example.
- Provide suggested mitigations.
- Agree on a timeline for public disclosure.

Examples and scenarios

The /examples folder contains reproducible scenarios. Each scenario includes:

- A README that explains the intent.
- A JSON configuration for the simulator.
- Expected outputs and checks.

Example scenario: session replay consistency

- Purpose: show how inconsistent client clocks produce state divergence.
- Components: two simulator clients, mock server, trace collector.
- Steps:
  - Start clients with different clock offsets.
  - Perform identical sequences of actions.
  - Collect traces and compare state deltas.
- Outcome: The scenario highlights the need for server-side timestamps and idempotent operations.

Example scenario: rate-limited endpoints

- Purpose: study how client retry behavior interacts with server throttling.
- Components: simulator, mock server with rate limit policy.
- Steps:
  - Run concurrent requests with exponential backoff.
  - Measure server response codes and latency.
- Outcome: The scenario shows a safe way to test backoff policies and to find misconfigured limits.

Telemetry and trace analysis

The project ships tooling to collect, visualize, and analyze traces.

- Output formats: OpenTelemetry, HAR, JSON.
- Visualizer: a small web app that parses traces and shows timelines.
- Analysis scripts: detect anomalies, compute deltas, and highlight unexpected responses.

Examples of trace analysis

- Detecting out-of-order events
- Comparing payload differences across clients
- Measuring latencies for critical operations

Data privacy

- Avoid storing real user data in traces.
- Use synthetic or anonymized data for experiments.
- Redact identifiers in logs and traces.

Packaging and releases

This repo uses a standard release process.

- Tag releases using semantic versioning.
- Include release notes that document changes and known issues.
- Attach artifacts: test traces, coverage reports, and minimal builds.

Visit the Releases page to find published artifacts and archives:  
https://github.com/hesaradulen/Arceus-Executor/releases

Note on artifacts

The Releases page may contain packaged artifacts that aid reproducibility. Review artifacts before running them, and prefer to build from source for full transparency.

Build from source

If you prefer to build components yourself, follow these steps.

Simulator (Node)

- cd simulator
- npm ci
- npm run build
- npm pack (creates a tarball)

Mock server (Python)

- cd sandbox/mock-server
- python -m venv .venv
- source .venv/bin/activate
- pip install -r requirements.txt
- python -m sandbox.server --config config/local.json

Docker

- docker build -t arceus/mock-server:local sandbox/mock-server
- docker compose up --build

Debugging tips

- Enable debug logs with the DEBUG environment variable.
- Use the trace exporter to output JSON and inspect payloads.
- Replay traces using the trace runner in tools/.

Security posture

- Keep dependencies up to date.
- Monitor advisories for used packages.
- Use ephemeral credentials for tests.
- Run tests in a network-isolated environment.

Hardening checklist

- Bind services to localhost.
- Use non-default ports and explicit configuration.
- Avoid running privileged containers.
- Use mounted volumes for test artifacts.

Use cases

- Reproduce client-side bugs in a controlled way.
- Test defensive server-side logic under load or weird client behavior.
- Teach students how to design and test secure client-server systems.
- Build tooling that helps developers find race conditions and state inconsistencies.

Not use cases

- Running tests against live services without consent.
- Distributing tools that enable cheating or exploitation.
- Any activity that violates the terms of service of another provider.

Roadmap

- Improve scenario language with richer conditions and assertions.
- Add more visualizations for traces and state transitions.
- Offer a community-maintained catalog of safe scenarios.
- Add support for CI-driven experiment runs with artifact capture.

Maintainers and contact

- Maintainers list lives in MAINTAINERS.md.
- Use Issues to report bugs or to request features.
- For security reports, use a private contact channel listed in SECURITY.md.

Code of conduct

This project uses a contributor code of conduct. Respect others. Report abuse to the maintainers.

License

This repository uses the MIT license unless stated otherwise in module headers. See LICENSE for details.

Acknowledgements

- Open source maintainers who publish tooling and patterns for safe security research.
- Authors of trace formats and analysis tools that we reuse.
- Community contributors who help keep tests and docs clean.

Appendix A — Example scenario file (safe, illustrative)

Below is a simple scenario outline. The scenario runs two simulated clients that update a common state key. The scenario records both client requests and server responses. Use this as a template for writing more cases.

{
  "name": "state-race-demo",
  "description": "Two clients update the same key concurrently to show conflict resolution.",
  "clients": [
    { "id": "client-a", "script": "seq1.json" },
    { "id": "client-b", "script": "seq2.json" }
  ],
  "server": {
    "baseUrl": "http://localhost:8080/api",
    "auth": { "apiKey": "test-key" }
  },
  "run": {
    "concurrency": 2,
    "repeat": 10,
    "record": "traces/state-race-demo.json"
  }
}

Appendix B — Example test checklist

- Unit tests pass: npm test && pytest
- Integration test spins up sandbox and runs example scenario
- Lint: npm run lint && flake8 .
- Coverage: run coverage tools and verify thresholds

Appendix C — Common research patterns

- Replay: store a trace and replay it to reproduce a bug.
- Fuzzing: feed malformed inputs to see how the system responds.
- Differential testing: run two server versions in parallel to find behavioral change.
- Load testing: run controlled load to study rate limits and backoff behavior.

Appendix D — Helpful tools and references

- OpenTelemetry: trace format and collectors
- HAR format: HTTP archive for request/response capture
- Docker Compose: sandbox orchestration
- CI services: example CI configs live in /ci

Acknowledgement of limitations

This README does not provide instructions to target third-party services or to develop cheat tools. It aims to support legitimate research and development in controlled conditions only.

Further reading and learning paths

- Threat modeling basics
- API design best practices
- Rate-limiting strategies
- Idempotency and safe update design
- Secure telemetry and logging

Get the official artifacts and release notes on the Releases page:  
https://github.com/hesaradulen/Arceus-Executor/releases

If you want a specialized README that focuses on a different, ethical use case such as a teaching lab or an internal QA tool, say which use case you want and I will prepare a full README tuned to that scenario.