# AI Engineering Portfolio

A build log of my 26-week journey through the **Agentic Engineer Program**. Each week I build something real, record what broke, and note what I learned.
## Progress

The program runs 26 weeks in six phases and produces 7 portfolio projects plus a bonus.

| Weeks | Phase | Project / focus | Status |
|-------|-------|-----------------|--------|
| 0 | Onboarding | Dev environment, custom AI agent with tracing, this portfolio | Done |
| 1-6 | Foundations | **#1 Digital Twin**: RAG chatbot that answers as me about my career, with citations | Planned |
| 7-8 | Agents | **#2 Researcher-Writer**: orchestrator plus research, writer and image agents | Planned |
| 9-10 | Agents | **#3 Multi-MCP Agent**: one agent using 3 MCP servers I write | Planned |
| 11-12 | Production | **#4 Full-Stack Ship**: Project #2 as a product (Next.js, FastAPI, streaming, sign-in, Stripe test mode, cost caps) | Planned |
| 13-14 | Production | **#5 MCP Agent on Azure**: Container Apps with Foundry, Key Vault, Managed Identity, App Insights | Planned |
| 15-16 | Production | **#6 Pipeline with Eval Gates**: Terraform (3 envs), GitHub Actions with eval gates, nightly evals | Planned |
| 17-22 | Capstone | **#7 Writer's Room Agent**: developmental editor, line editor and continuity checker agents (suggest-only) | Planned |
| 21-23 | Capstone / Career | **Bonus: AI Readiness Calculator**: assessment web app that scores AI readiness | Planned |
| 23-26 | Career | Portfolio polish: GitHub and LinkedIn profiles, demos and write-ups | Planned |

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
git clone https://github.com/hoovajr/custom-ai-agent.git
cd custom-ai-agent
python -m venv .venv
.venv\Scripts\Activate.ps1        # Windows PowerShell
pip install -r requirements.txt
copy .env.example .env            # then add OPENAI_API_KEY (and Langfuse keys, optional)
python -m unittest discover -s tests
```

### Links

- Agent project: [hoovajr/custom-ai-agent](https://github.com/hoovajr/custom-ai-agent) (private)
- Langfuse trace screenshot: _add to `docs/images/`_

## Up next

- Task memory, tools and prompts for the agent
- Streaming responses
- Move prompts into Langfuse prompt management

## About

Built by [@hoovajr](https://github.com/hoovajr) as part of the Agentic Engineer Program. Entries are added weekly.
