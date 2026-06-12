# Advanced Curriculum: Building a Team of AI Agentic Engineers + Forward-Deployed Engineers (FDEs)

## TL;DR

- This is a 14-module, ~16-week advanced program (dual-track, ~50/50 agentic engineering and FDE craft) built on three primary ecosystems — Anthropic Claude API + MCP, OpenAI Agents SDK, and AWS Bedrock/AgentCore — with a standalone advanced lab in every module plus two integrative capstones; budget roughly 14–18 hours/week.
- The agentic-engineering track teaches the patterns AI labs actually ship in 2025–2026: orchestrator-worker/supervisor multi-agent systems, context engineering, tool design, MCP servers, evals/observability (OpenTelemetry GenAI conventions, Langfuse/LangSmith/Braintrust), guardrails, and cost/latency optimization (prompt caching, model routing).
- The FDE track codifies the Palantir-origin model now adopted by Anthropic's Applied AI team and OpenAI: embedded discovery (30–50 interviews in weeks 1–2), tight 2–6 week build sprints, deploy-in-customer-VPC mechanics, eval co-design with customer SMEs, stakeholder management, and productization feedback loops — measured on a deal-velocity + productization-rate scorecard rather than billable hours.

## Key Findings

**The two roles are converging and both are in extreme demand.** FDE job postings soared by more than 800% between January and September 2025 according to analysis by Indeed and the Financial Times — and the imbalance is acute: per Paraform (citing Live Data Technologies), "Between January and September 2025, job postings for FDEs grew 800%. The candidate pool? It grew about 50%." OpenAI built its FDE team starting in 2024; Anthropic's Applied AI team explicitly hires "Forward Deployed Engineer" roles whose deliverables are "MCP servers, sub-agents, and agent skills that will be used in production workflows," with Anthropic advertising FDE positions in the $200,000–$300,000 base range (per BigGo Finance). A curriculum that produces engineers who can both _build_ agentic systems and _deploy them inside messy customer environments_ targets exactly the profile these labs are competing for.

**The dominant production architecture is orchestrator-worker, not autonomous swarms.** Anthropic's own Research system uses a lead agent that "spins up 3-5 subagents in parallel rather than serially" (with subagents using "3+ tools in parallel"), plus a separate CitationAgent; per Anthropic Engineering, "a multi-agent system with Claude Opus 4 as the lead agent and Claude Sonnet 4 subagents outperformed single-agent Claude Opus 4 by 90.2% on our internal research eval," cutting research time "by up to 90% for complex queries." The tradeoff: multi-agent systems "use about 15× more tokens than chats," and token usage explained 80% of performance variance on BrowseComp. The cross-cutting lesson from Anthropic, AWS, and OpenAI alike: start simple, add agents only when the task decomposes into independent parallel threads, and treat context as a finite resource.

**Tooling has standardized fast.** MCP is now the universal tool-integration layer (adopted by OpenAI, AWS, Google). OpenTelemetry GenAI semantic conventions became the production tracing standard in late 2025. The Responses API is OpenAI's stateful, agentic default. Amazon Bedrock AgentCore reached general availability on October 13, 2025 across nine AWS Regions, and added Policy (preview) and Evaluations (preview, with 13 built-in evaluators) on December 2, 2025. A curriculum frozen to one vendor's 2024 API would already be stale.

**Evaluation is the new QA, and it is the FDE's core technical artifact.** For agents you must evaluate trajectories (tool calls, reasoning steps), not just final answers, using deterministic checks where possible and LLM-as-judge for subjective dimensions. In FDE practice, "the eval set IS the test suite" — discovery interviews seed the eval cases, and every production failure becomes a regression test.

## Details — Full Modular Course Outline

### Program design

- **Audience & prerequisites:** MS-level CS or equivalent. Assumed: strong Python (and ideally TypeScript), distributed systems, REST/async, containers (Docker), cloud IAM basics, and ML/LLM fundamentals (tokenization, embeddings, transformers, fine-tuning vs. prompting). No prior agent-framework experience required.
- **Structure:** Two parallel tracks run throughout — **Track A: Agentic Systems Engineering** and **Track B: Forward-Deployed Engineering Craft** — mirroring how real FDEs operate (technical depth + delivery ownership). Each module is standalone with its own lab.
- **Assessment:** (1) Per-module lab rubric (objectives + success criteria below); (2) two integrative capstones; (3) a "productization memo" and a recorded stakeholder demo for the FDE track; (4) an eval-quality gate — labs are graded partly on whether the learner built a passing eval suite, not just working code.
- **Sequencing:** Modules 1–7 establish agentic foundations and the three ecosystems; Modules 8–10 cover production hardening (evals, observability, security, cost); Modules 11–13 cover the FDE craft end-to-end; Module 14 = capstones. Track B threads (discovery, stakeholder comms) are introduced early (Module 1) so learners practice them across every later build.

---

### Module 1 — Agent Foundations & the FDE Operating Model

**Track:** A + B
**Learning objectives:** Distinguish workflows from agents and know when each is appropriate; understand the agent loop (model → tool call → result → loop); articulate the FDE model and engagement lifecycle.
**Key concepts:** Anthropic's "Building Effective Agents" taxonomy (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer); augmented-LLM building block; agent-computer interface (ACI); "do the simplest thing that works." FDE model: Palantir's Delta/Echo team structure, "one customer, many capabilities," gravel-roads-to-paved-highways productization loop; how Anthropic/OpenAI adapted it.
**Hands-on lab:** Implement the five Anthropic workflow patterns from scratch against a raw LLM API (no framework) — prompt chaining with a validation gate, a router, parallelized voting, an orchestrator-worker, and an evaluator-optimizer loop. Then write a 1-page "agent vs. workflow" decision memo for a given business problem.
**Success criteria:** All five patterns run and pass unit tests; decision memo correctly identifies which pattern fits and justifies the latency/cost tradeoff; learner can explain the agent loop without reference to a framework.
**Resources:** Anthropic "Building Effective AI Agents" (research + the architecture-patterns PDF); Pragmatic Engineer "What are Forward Deployed Engineers"; SVPG "Forward Deployed Engineers."
**Effort:** ~10 hours.

---

### Module 2 — The Anthropic Claude Platform: Messages API, Tool Use, Extended Thinking, Prompt Caching

**Track:** A
**Learning objectives:** Master Claude's Messages API tool-use loop; use extended thinking correctly; engineer prompt caching for cost/latency.
**Key concepts:** Messages API structure; tool_use / tool_result blocks; parallel tool use; strict tool use; extended thinking and its interaction with tool use; prompt caching mechanics (cache_control ephemeral breakpoints, up to 4 breakpoints, prefix order tools→system→messages, 5-min vs 1-hr TTL, cache writes cost 25% more / reads cost 10% of base; thinking-budget changes invalidate message-prefix caches but not cached system/tools). Cost telemetry fields: cache_creation_input_tokens, cache_read_input_tokens.
**Hands-on lab:** Build a tool-using research assistant on the raw Messages API with 3+ tools, extended thinking enabled, and an optimized caching strategy. Instrument it to log per-request cache hit rate and cost. Run a before/after experiment showing caching reduces cost on a multi-turn conversation.
**Success criteria:** Demonstrated cache-read hit rate >70% on repeated-prefix turns; correct handling of thinking blocks across tool-use turns; measured cost reduction documented with the usage fields.
**Resources:** Claude API docs (Prompt caching, Extended thinking, Tool use); Anthropic "Writing effective tools for AI agents."
**Effort:** ~12 hours.

---

### Module 3 — The Claude Agent SDK, Subagents, Skills & Computer Use

**Track:** A
**Learning objectives:** Build production agents on the Claude Agent SDK; author Agent Skills; understand computer use and its risks.
**Key concepts:** Claude Agent SDK (renamed from Claude Code SDK in Sept 2025) — `query()` vs `ClaudeSDKClient`, the built-in agent loop and context management, in-process SDK MCP servers via `@tool` + `create_sdk_mcp_server`, hooks, permissions (allowed_tools/disallowed_tools), subagents, sessions (SessionStore), max_budget_usd. Agent Skills: SKILL.md progressive disclosure, the open standard (published Dec 2025), code-as-tools, skills via the API (code-execution + skills beta headers). Computer use tool: screenshot/mouse/keyboard agent loop, sandboxed Docker reference implementation, prompt-injection risk.
**Hands-on lab:** Build a domain-specific agent with the Claude Agent SDK that (a) exposes 2 custom in-process tools, (b) loads a custom Skill you author, and (c) runs under a tool-permission policy and a spend cap. Bonus: add a computer-use step in a sandboxed container.
**Success criteria:** Agent completes a multi-step task autonomously; Skill is triggered only when relevant (progressive disclosure verified in traces); permission policy blocks a disallowed tool; spend cap enforced.
**Resources:** Claude Agent SDK overview & GitHub (Python/TS); Anthropic "Building agents with the Claude Agent SDK"; "Equipping agents for the real world with Agent Skills"; Computer use tool docs.
**Effort:** ~14 hours.

---

### Module 4 — Model Context Protocol (MCP): Servers, Clients, Primitives & Transports

**Track:** A
**Learning objectives:** Explain MCP's host/client/server architecture; build production MCP servers and clients; choose transports correctly.
**Key concepts:** Host/client/server roles; JSON-RPC 2.0 data layer; lifecycle/capability negotiation; server primitives (tools, resources, prompts); client primitives (roots, sampling, elicitation); transports — STDIO (local, one client per process) vs Streamable HTTP (remote, many clients; recommended for production; stateless_http + json_response for scale); SSE deprecated. FastMCP (now the high-level interface bundled in the official Python SDK) — `@mcp.tool/@mcp.resource/@mcp.prompt` decorators, auto-schema from type hints; MCP Inspector for debugging; namespacing tools (e.g., asana_search, jira_search); context-efficient tool responses (Claude Code caps tool responses at 25,000 tokens).
**Hands-on lab:** Build a production MCP server exposing tools + resources + a prompt for a real backend (e.g., a database or internal API), with Streamable HTTP transport and an evaluation harness. Then build a minimal MCP client that discovers and calls it. Write tool descriptions optimized for agent selection and prove improvement with a tool-use eval.
**Success criteria:** Server passes MCP Inspector validation; client successfully lists and invokes tools/resources/prompts; tool-use eval shows measurable improvement after description/namespacing optimization; responses are paginated/truncated to stay context-efficient.
**Resources:** modelcontextprotocol.io architecture docs; MCP Python SDK + FastMCP (gofastmcp.com); Anthropic "Writing effective tools for AI agents."
**Effort:** ~14 hours.

---

### Module 5 — MCP Security & Trust

**Track:** A + B
**Learning objectives:** Identify and mitigate MCP-specific attack surfaces; deploy MCP servers safely in customer environments.
**Key concepts:** Prompt injection (OWASP #1 LLM vulnerability 2025); tool poisoning (malicious instructions in tool metadata; persists across sessions); indirect/cross-server injection; documented incidents and CVEs (e.g., CVE-2025-6514 in mcp-remote, CVSS 9.6; the Supabase Cursor agent incident, June 2025; Invariant Labs' WhatsApp exfiltration demo); the spec's "human in the loop SHOULD deny tool invocations" gap; defenses — client-side validation of server metadata, sandboxing, least-privilege, human-in-the-loop approvals, audit logging, supply-chain verification, OAuth 2.1 for remote servers.
**Hands-on lab:** Red-team then harden an MCP deployment. Given a deliberately vulnerable MCP server, demonstrate a tool-poisoning and an indirect-injection attack, then implement mitigations (metadata validation, allow-listing, human-approval gating on side-effecting tools, structured logging) and re-test.
**Success criteria:** Both attacks reproduced and documented; mitigations verifiably block them; learner produces a 1-page MCP threat model (STRIDE-style) for a customer deployment.
**Resources:** Cloud Security Alliance "Agentic MCP Security Best Practices"; OWASP Top 10 for LLM Applications 2025; published MCP threat-modeling papers.
**Effort:** ~10 hours.

---

### Module 6 — OpenAI Agents SDK & the Responses API

**Track:** A
**Learning objectives:** Build multi-agent systems with the OpenAI Agents SDK; use the Responses API for stateful agents; compare the OpenAI and Anthropic approaches.
**Key concepts:** Agents SDK primitives — agents, handoffs (control transfer, not function return), guardrails (input/output/tool; tripwires; parallel vs blocking execution; input guardrails run only on first agent, output only on final), sessions (automatic history), tracing (built-in, on platform.openai.com; spans for LLM calls/tool calls/handoffs/guardrails), function tools, hosted tools, MCP support, sandbox agents, human-in-the-loop (needs_approval, interruptions, resumable state). Responses API — stateful (store + previous_response_id or Conversations API), agentic loop with built-in tools, preserves reasoning across turns, 40–80% better cache utilization vs Chat Completions; ZDR via encrypted reasoning items. Compare to Claude Messages API.
**Hands-on lab:** Build a customer-support triage system with a router agent that hands off to 2–3 specialist agents, input/output guardrails (cheap classifier model) that fail-fast, and tracing. Then re-implement one specialist's loop on the raw Responses API to understand what the SDK abstracts.
**Success criteria:** Handoffs correctly transfer control; a malicious input trips the guardrail before the expensive model runs; traces clearly show the decision path; learner can articulate 3 concrete differences between the OpenAI and Anthropic agent models.
**Resources:** OpenAI Agents SDK docs (agents, handoffs, guardrails, tracing); "Why we built the Responses API"; OpenAI migrate-to-Responses guide.
**Effort:** ~14 hours.

---

### Module 7 — AWS Bedrock & AgentCore: Enterprise Agent Deployment

**Track:** A + B
**Learning objectives:** Deploy agents on AWS with enterprise security; use Bedrock Agents and AgentCore components; invoke Claude on Bedrock correctly.
**Key concepts:** Invoking Claude on Bedrock — model IDs (e.g., `anthropic.claude-sonnet-4-5-20250929-v1:0`), cross-region inference profiles (us./eu./global. prefixes), `bedrock:InvokeModel` IAM on both inference-profile and foundation-model ARNs, the Converse API. Bedrock Agents (original) — action groups (OpenAPI or function schema + Lambda), knowledge bases (RAG), multi-agent collaboration (supervisor vs supervisor-with-routing modes), service-role trust + Lambda resource-based policy. AgentCore (GA Oct 13, 2025, in nine Regions) components: Runtime (serverless, Firecracker microVM session isolation, 8-hour max execution / 15-min idle default, ARM64 container contract on port 8080, any framework — LangGraph/CrewAI/Strands/LlamaIndex/OpenAI Agents SDK, MCP+A2A+HTTP protocols, AgentCore CLI deploy); Gateway (turns Lambda/OpenAPI/Smithy/MCP servers into MCP tools, inbound/outbound auth, semantic tool selection); Memory (short-term + long-term semantic/user-preference/summary/episodic strategies); Identity (workload identities, OAuth token vault); Observability (CloudWatch + OpenTelemetry/ADOT); Browser + Code Interpreter sandboxes; Policy (Cedar-based real-time tool-call gating) and Evaluations (13 built-in evaluators) added Dec 2, 2025.
**Hands-on lab:** Deploy a multi-agent system on AWS two ways: (1) a Bedrock Agents supervisor with two specialist collaborators each backed by a knowledge base; (2) the same agent logic deployed to AgentCore Runtime with a Gateway-exposed tool (from a Lambda), Memory enabled, and Observability wired to CloudWatch. Lock down IAM to least privilege.
**Success criteria:** Both deployments answer domain-segmented queries correctly; IAM policy is least-privilege (scoped to specific inference-profile + FM ARNs and agent ARNs); AgentCore traces visible in CloudWatch; Gateway tool callable via MCP.
**Resources:** AWS AgentCore dev guide; "Introducing Amazon Bedrock AgentCore"; AWS multi-agent collaboration docs; AgentCore Starter Toolkit / AgentCore CLI.
**Effort:** ~16 hours.

---

### Module 8 — Context Engineering, Memory & Retrieval for Agents

**Track:** A
**Learning objectives:** Engineer agent context as a finite resource; design memory systems; build retrieval that serves agents (not just chatbots).
**Key concepts:** Context engineering (smallest high-signal token set; "right altitude" system prompts; avoid brittle hardcoded logic); compaction and context editing; just-in-time vs upfront retrieval (Claude Code's hybrid: CLAUDE.md upfront + glob/grep on demand); external memory to persist state beyond the context window (Anthropic Research persists plans when >200K tokens); agentic search vs semantic search tradeoffs; memory systems (short-term/long-term/episodic — cf. AgentCore Memory strategies); retrieval augmentation patterns, chunking, vector DBs (pgvector/Pinecone/Weaviate), reranking.
**Hands-on lab:** Build an agent with a 3-tier memory system (working/session/long-term) over a large corpus, using a hybrid retrieval strategy. Demonstrate it maintaining coherence across a task that exceeds the context window via external-memory persistence and compaction.
**Success criteria:** Agent completes a >200K-token-equivalent task without losing its plan; retrieval precision measured and tuned; learner documents the upfront-vs-just-in-time decision and its latency impact.
**Resources:** Anthropic "Effective context engineering for AI agents" + memory/context-management cookbook; Anthropic multi-agent research write-up; AgentCore Memory docs.
**Effort:** ~14 hours.

---

### Module 9 — Agent Evaluation: Trajectories, LLM-as-Judge & Regression Suites

**Track:** A + B
**Learning objectives:** Build multi-level agent evals; design rubrics and calibrate LLM-as-judge; turn production failures into regression tests.
**Key concepts:** Three eval levels — end-to-end (task success), trajectory (tool calls + reasoning path efficiency/soundness), component (which retriever/tool/sub-agent broke). Metric families: tool calling, planning, task completion, reasoning, plus safety/latency/cost. Deterministic checks for exact tool names/params; LLM-as-judge (pointwise/pairwise/checklist; G-Eval; rubric design; scale drift; calibration against human labels) for subjective dimensions. Agent-as-a-Judge for full-trajectory evaluation. Building eval datasets with ground-truth tool calls; growing the dataset from every failure.
**Hands-on lab:** Build a complete eval pipeline for one of your earlier agents: a ground-truth dataset (with expected tool calls), deterministic trajectory checks, a calibrated LLM-as-judge for reasoning quality, and CI integration that fails the build on regression. Calibrate the judge against ≥20 human-labeled examples.
**Success criteria:** Eval suite runs in CI and catches an intentionally injected regression; LLM-judge agreement with human labels reported (target meaningful correlation); dataset includes both expected outputs and expected trajectories.
**Resources:** LangChain "LLM Evaluation Framework: Trajectories vs Outputs"; Confident AI agent-eval guide; "Agent-as-a-Judge" paper; Anthropic tool-evaluation cookbook.
**Effort:** ~14 hours.

---

### Module 10 — Production Observability, Cost/Latency Optimization & Incident Response

**Track:** A + B
**Learning objectives:** Instrument agents with standards-based observability; optimize cost and latency; run incident response for non-deterministic systems.
**Key concepts:** OpenTelemetry GenAI semantic conventions (the late-2025 standard — LLM-call spans, tool-execution spans as first-class); observability platforms — Langfuse (open-source/self-host, MIT, acquired by ClickHouse Jan 2026), LangSmith (LangChain-native), Braintrust (eval-first), Arize Phoenix (OTel-native), Laminar; dual-export pattern (AI-native tool + Datadog/Grafana). Cost/latency: prompt caching, model routing/cascades (cheap classifier → expensive model), batch processing, plan-and-execute to cut tokens, the multi-agent ~15× token multiplier and when it's justified, token-budget/latency limits before shipping. Incident response: tracing as root-cause tool, the "stale tool data" failure class, versioning prompts, A/B testing, monitoring drift, online evals.
**Hands-on lab:** Instrument a multi-agent system end-to-end with OpenTelemetry GenAI conventions exporting to Langfuse (or Phoenix), build a cost/latency dashboard with per-trace attribution, then run a simulated incident: a 3–4% intermittent failure you must diagnose from traces alone and fix. Implement one cost optimization and measure the savings.
**Success criteria:** Every LLM call, tool call, and retrieval is a first-class span; learner diagnoses the injected failure from traces; documented cost reduction (e.g., via caching or routing) with before/after numbers; prompt versioning in place.
**Resources:** OpenTelemetry GenAI conventions; Langfuse/LangSmith/Braintrust/Phoenix docs; AgentCore Observability (OTEL/CloudWatch).
**Effort:** ~14 hours.

---

### Module 11 — FDE Craft I: Discovery, Scoping & Requirements

**Track:** B
**Learning objectives:** Run customer discovery like an FDE; scope engagements tightly with exit criteria; translate fuzzy executive goals into eval-ready engineering tasks.
**Key concepts:** Discovery as a first-class layer (FDEs spend up to ~40% of time here; 30–50 interviews in first two weeks); the four-week discovery loop (Week 0 stakeholder map + eval-customer split; Weeks 1–2 user interviews at scale; Weeks 3–4 eval co-design with customer SMEs; continuous post-launch feedback); surfacing latent requirements (regulatory edge cases, legacy data quirks, undocumented workarounds); what to ask end-users vs admins vs executive sponsors; scoping to one business bottleneck with measurable outcomes before build; written, time-bounded, product-aligned engagement contracts; entry/exit criteria to prevent scope creep; the 180-day "no one defined done" failure mode.
**Hands-on lab:** Run a simulated discovery engagement (instructor/peers role-play stakeholders): produce a stakeholder map, conduct structured interviews with three personas, and deliver a scoping document with (a) the single bottleneck, (b) measurable success metrics, (c) an eval-customer split, and (d) explicit exit criteria. Convert interview findings into an initial eval dataset.
**Success criteria:** Scoping doc ties every requirement to a measurable outcome and a stakeholder; latent requirements surfaced that weren't in the original brief; eval dataset seeded directly from interview language and edge cases.
**Resources:** Perspective AI "How FDEs Run Customer Discovery"; Palantir "Day in the Life of an FDSE"; a16z on AI deployment requiring read/write access + rich context; Barnacle Labs two-week scoping write-up.
**Effort:** ~12 hours.

---

### Module 12 — FDE Craft II: Rapid Prototyping & Deploying in Customer Infrastructure

**Track:** B
**Learning objectives:** Prototype fast inside customer constraints; deploy into customer cloud/VPC with their security posture; build bespoke solutions on top of platforms.
**Key concepts:** "Hackathon inside the firewall" prototyping; the FDE deploy reality — n productions, each its own environment/VPC/possibly own model weights (the 12-factor "single production" assumption breaks); deploying in customer cloud (in-VPC AWS/GCP/Azure, AgentCore VPC/PrivateLink, Bedrock data residency via geo inference profiles); the modern FDE stack (build: Claude Code/Cursor/LangSmith/Braintrust; deploy: Modal/Vercel/customer cloud; observability: Datadog/Honeycomb; iteration: prompt versioning/telemetry); building bespoke artifacts on a platform (MCP servers, sub-agents, Skills as delivery artifacts — exactly Anthropic FDE deliverables); compliance/security in customer contexts (data handling, ZDR, least-privilege IAM, audit). Knowledge transfer so the customer can own/extend (the FIS/Anthropic "co-design and transfer knowledge" model).
**Hands-on lab:** Take an agent you built earlier and "deploy it as an FDE": containerize it, deploy to a simulated customer VPC (or AgentCore Runtime with VPC/PrivateLink + least-privilege IAM), wire customer-scoped secrets, set up per-customer observability, and write a runbook + knowledge-transfer doc so the "customer team" can operate it.
**Success criteria:** Agent runs in an isolated, least-privilege customer-style environment; secrets are customer-scoped; a non-author can operate it from the runbook; deliverables include at least one reusable artifact (MCP server / sub-agent / Skill).
**Resources:** Perspective AI "FDE Tech Stack in 2026"; AWS AgentCore enterprise security (VPC/PrivateLink); Anthropic FDE job description (deliverables); CIO/FIS Financial Crimes agent case.
**Effort:** ~16 hours.

---

### Module 13 — FDE Craft III: Stakeholder Management, Demos & Productization

**Track:** B
**Learning objectives:** Manage stakeholders and executives; run workshops and demos; measure outcomes; feed insights back to product.
**Key concepts:** Executive communication and translating technical concepts to diverse stakeholders (an explicit Anthropic FDE requirement); running workshops; navigating organizational politics ("startup CTO" ownership profile); change management (AI deployment is fundamentally an organizational-change problem); demoing and iterating fast with customers; measuring outcomes (the four-metric scorecard: deal velocity ≤90 days to production, net revenue retention, productization rate ≥1 feature/engagement, reusable-asset ratio); the productization loop (weekly "what I built that should be a feature" review); avoiding the consulting trap (tracking only billable/deal metrics starves product). Managing the FDE dependency risk customers worry about (the CIO/Greyhound "dependency with better stationery" critique).
**Hands-on lab:** Deliver a 20-minute stakeholder demo of a capstone-track build to a mixed "executive + end-user + skeptical-IT" panel, handle objections live, then write a productization memo identifying which parts of the bespoke build should become reusable product features and why, with a four-metric scorecard for the engagement.
**Success criteria:** Demo lands the business outcome (not the tech) for a non-technical sponsor; objections handled; productization memo identifies ≥1 generalizable feature and justifies it against the scorecard.
**Resources:** Perspective AI founder's FDE playbook (scorecard); The New Stack / MarkTechPost on AI-lab FDEs; CIO piece on FDEs as the AI "limiting factor"; Palantir productization (gravel roads → paved highways).
**Effort:** ~10 hours.

---

### Module 14 — Integrative Capstones

**Track:** A + B
**Learning objectives:** Integrate the full stack under realistic constraints.

**Capstone 1 (Agentic Engineering):** Design, build, evaluate, and deploy a production-grade multi-agent system on one primary ecosystem of choice (Claude Agent SDK + MCP, OpenAI Agents SDK, or AWS AgentCore), with: orchestrator-worker architecture, ≥2 MCP-exposed tools, a 3-tier memory system, guardrails, a CI eval suite (trajectory + LLM-judge), OpenTelemetry observability, and a documented cost/latency optimization. **Success criteria:** Passes its own eval gate in CI; traces and cost dashboard present; defensible architecture decision record; security threat model included.

**Capstone 2 (FDE Engagement Simulation):** Run a full simulated engagement end-to-end: discovery → scoping doc with exit criteria → rapid prototype → deploy in a customer-style VPC with least-privilege IAM → eval co-designed with "customer SMEs" → stakeholder demo → knowledge-transfer runbook → productization memo + four-metric scorecard. **Success criteria:** A non-author "customer" can operate the system from the runbook; the eval suite reflects real interview-sourced edge cases; the productization memo and scorecard demonstrate FDE (not consulting) thinking.

**Effort:** ~30–40 hours combined.

## Recommendations

1. **Run the two tracks in parallel from day one, not sequentially.** The whole point of the FDE-meets-agentic-engineer profile is that discovery, evals, and stakeholder communication are practiced _while_ building. Introduce Module 11's discovery skills early and require learners to apply them to every later build's requirements.

2. **Make evals the spine of the program.** Grade labs partly on the eval suite, not just working code — this mirrors how AI-lab FDEs work ("the eval set IS the test suite") and forces the trajectory-evaluation discipline that separates production-grade engineers from demo-builders. Threshold to advance past Module 9: a CI eval suite that catches an injected regression.

3. **Teach one ecosystem deeply, then generalize.** Have each learner pick a primary stack for the capstone (Claude+MCP, OpenAI Agents SDK, or AgentCore) but require them to articulate the cross-cutting patterns (orchestrator-worker, MCP as the tool layer, OTel as the trace layer) so they can redeploy skills as APIs churn.

4. **Stage the rollout for a team:** Weeks 1–4 (Modules 1–4) build shared agentic + MCP foundations; Weeks 5–7 (Modules 5–7) cover security and all three ecosystems; Weeks 8–10 (Modules 8–10) harden for production; Weeks 11–13 (Modules 11–13) build the FDE craft; Weeks 14–16 capstones. Adjust for cohort prior experience.

5. **Benchmarks that should change the plan:** If learners can't reach a >70% cache-hit rate (M2) or a CI eval that catches regressions (M9), slow down before advancing — these are leading indicators of production readiness. If a cohort already ships agents in production, compress Modules 1–6 and weight toward Modules 9–13 (evals, observability, FDE craft), which is where most teams are actually weak.

6. **Keep the curriculum on a quarterly refresh cycle.** The APIs and tooling here moved substantially within 2025–2026 (Agent SDK rename, Responses API, AgentCore GA + Policy/Evaluations, MCP transport changes, OTel GenAI conventions). Treat version-specific lab instructions as perishable.

## Caveats

- **Fast-moving APIs:** Model IDs, beta headers, SDK method names, and pricing multipliers cited here are current to ~2025–2026 and will drift; verify against official docs before each cohort. Several specifics (e.g., the Claude Agent SDK June 15, 2026 metering change; AgentCore Policy/Evaluations preview→GA timing — Evaluations reached GA on March 31, 2026, having launched in preview Dec 2, 2025 in four Regions: N. Virginia, Oregon, Sydney, Frankfurt) should be re-checked for production behavior and region availability.
- **Vendor framing vs. independent evidence:** Performance claims such as Anthropic's "90.2% improvement" / "~15× tokens" and AWS's cost/latency figures come from vendor sources and internal evals; treat them as directional, not independently verified benchmarks. The "13 built-in AgentCore evaluators" count is AWS-authoritative but the only public enumeration of names slightly exceeds 13 — confirm the exact list against the Evaluations API docs before publishing it as fact.
- **FDE compensation and demand figures** come from recruiting/industry sources and vary by stage, geography, and seniority. The 800%+ posting-growth figure is attributed to Indeed and the Financial Times (Indeed's underlying data shows postings rising from 643 in April 2025 to 5,330 in April 2026, ~729% YoY); Anthropic's advertised FDE range of $200K–$300K base is per BigGo Finance, while Indeed pegs the broader market "roughly between $170,000 and more than $200,000." Use as context, not guarantees.
- **"Claude Agent SDK on AgentCore"** is supported under AgentCore's "any framework" model but is not separately enumerated as a first-class integration in AWS docs the way LangGraph/CrewAI/Strands/OpenAI Agents SDK are — present accordingly.
- **Simulated engagements** can't fully replicate real customer politics, classified/regulated data constraints, or the emotional load of embedded delivery; supplement with real or partner-sourced engagements where possible.
- This curriculum assumes access to paid API credits (Anthropic, OpenAI) and an AWS account with Bedrock/AgentCore enabled; budget for non-trivial token spend, especially the multi-agent and eval-heavy modules.
