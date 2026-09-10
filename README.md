# FORGE
**Fault Observation & Repair through Generative Engineering**

FORGE is a Java 25 + Spring Boot + Spring AI capstone demonstrating a bounded coding agent:

**goal → retrieve context → model proposes one action → harness checks permission → tool executes → evidence returns to context → repeat → Maven verification → immutable-file check → independent AI audit**

## Why this design
The model does not receive a raw terminal. It can only choose from a small structured action vocabulary. The Java harness owns permissions and executes bounded tools.

Protected paths (tests, build file, Git metadata, wrappers, CI) are SHA-256 snapshotted before and after the run. If any protected file changes, the run is rejected even if Maven is green.

Repository text is explicitly treated as untrusted data to reduce prompt-injection risk.

The final LLM auditor is intentionally *not* treated as proof of safety; deterministic checks run first.

## Concepts demonstrated
- Spring AI `ChatClient`
- structured model outputs mapped directly to Java records
- agent loop / harness / tools / permissions
- retrieval-augmented repository grounding
- local tools with `@Tool`
- LLM-as-a-judge as an additional guardrail
- Java 25 virtual thread used for process-output collection
- Maven verification as machine-readable feedback
- SHA-256 test/build-policy integrity check
- Git diff evidence
- safe path canonicalisation and traversal prevention
- repository `AGENTS.md` guidance

## Local tools vs MCP
FORGE V1 deliberately runs its repository tools in-process for a reliable capstone demo. That is **local tool calling, not MCP**. The `RepositoryTools` boundary is the natural place to expose equivalent operations through a separate MCP server later.

## Setup
1. Install Java 25, Maven, and Git.
2. Keep the included `forge-demo-target` folder inside the FORGE project root.
3. Set your OpenAI key:
   - PowerShell: `$env:OPENAI_API_KEY="..."`
4. If necessary, edit `forge.target-repository` in `src/main/resources/application.yml`.
5. Build:
   `mvn clean verify`
6. Start:
   `mvn spring-boot:run`
7. Open `http://localhost:8080`.

## Recommended demo prompt
`Diagnose this repository, repair the failing behaviour, do not modify tests, and prove the change works.`

## Demo choreography
1. Show `forge-demo-target/src/test/.../GreetingServiceTest.java`.
2. Show that the test is red with `mvn clean verify`.
3. Launch FORGE.
4. Submit the mission.
5. Narrate the visible loop: baseline → retrieval → read/search → source patch → verify → diff → auditor.
6. Highlight that `src/test` hashes are unchanged.
7. Finish on `SUCCESS`.

## Troubleshooting
- If the configured chat model is unavailable to your API account, change `spring.ai.openai.chat.options.model` in `application.yml` to a model enabled for your key.
- If your Spring AI workshop starter uses a different Spring AI version, keep your known-good version and copy the FORGE source into that project.
- Do not point FORGE at a work repository with secrets or valuable uncommitted changes for the capstone. Use the supplied demo target.

