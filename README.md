# AI Engineering Portfolio

A public build log of my 26-week journey through the **Agentic Engineer Program**. Each week I build something real, record what broke, and note what I learned.

## Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| 0 | Setup and foundations | Dev environment, custom AI agent with tracing, this portfolio | Done |
| 1-25 | _Per the curriculum_ | _Added as I go_ | Planned |
| 26 | Capstone | _TBD_ | Planned |

## Week 0: Foundations

**Goal:** Get a working environment and ship a first agent with real observability, not just a hello-world.

### What I built

A custom AI agent in Python that calls the OpenAI chat completions API and is fully traced in Langfuse.

- **Agent core** (`src/agent.py`): an `Agent` class with a configurable name and system prompt. `respond()` validates input, calls OpenAI, and returns the reply.
- **Observability** (`src/tracing.py`): Langfuse client setup. Every call produces a `generate-response` generation with the input, the output, the model name, token usage for cost tracking, and agent metadata. `user_id` and `session_id` group traces by user and conversation.
- **Tests** (`tests/test_agent.py`): the OpenAI client is injected and mocked, so the suite runs free and offline.
- **Config hygiene**: credentials live in `.env` (git-ignored); `.env.example` documents the required keys. Without Langfuse keys, tracing is a safe no-op.

### Stack

Python, OpenAI API, Langfuse (v4, OpenTelemetry-based), python-dotenv, `unittest`, a `.venv` virtual environment, Git/GitHub.

### Build log

1. **Dev environment.** Created a Python virtual environment and confirmed `.env` stays in the project root (where `python-dotenv` looks for it), never inside the disposable `.venv`.
2. **Tracing first.** Added Langfuse to `Agent.respond()` before connecting a real LLM, so every later change was observable from the start. Verified a live trace arrived with complete metadata.
3. **Errors are traces too.** Moved input validation inside the trace span, so failed calls (such as an empty message) are recorded with an `ERROR` level and status message instead of vanishing.
4. **Real LLM.** Replaced the mock reply with an OpenAI chat completions call. Used dependency injection for the client so tests need no network or API key.
5. **End to end.** Ran a live OpenAI call and confirmed in Langfuse that the generation carried the correct model, token usage and cost.

### What went wrong, and what I learned

- **Tooling lagged the SDK.** `langfuse-cli` used a deprecated v3 endpoint that Langfuse v4 no longer serves, and the single-observation endpoint is deprecated too. Lesson: check the SDK version against its docs and tooling before trusting a tool's output.
- **Dependency conflict warning** appeared on installing Langfuse 4.15.6, though the tests still passed. Lesson: pin and test, and don't ignore warnings.
- **Venv `.gitignore`.** Python 3.13+ writes its own `.gitignore` inside `.venv`. It is harmless and overlaps the root one.
- **Observability pays off early.** Tracing from day one made the error-path bug visible immediately.

### Run it

```bash
git clone <agent-repo-url>
cd <agent-repo>
python -m venv .venv
.venv\Scripts\Activate.ps1        # Windows PowerShell
pip install -r requirements.txt
copy .env.example .env            # then add OPENAI_API_KEY (and Langfuse keys, optional)
python -m unittest discover -s tests
```

### Links

- Agent project: _link to the agent repo, or `projects/week-0-custom-agent/`_
- Langfuse trace screenshot: _add to `docs/images/`_

## Up next

- Task memory, tools and prompts for the agent
- Streaming responses
- Move prompts into Langfuse prompt management

## About

Built by [@hoovajr](https://github.com/hoovajr) as part of the Agentic Engineer Program. Entries are added weekly.
