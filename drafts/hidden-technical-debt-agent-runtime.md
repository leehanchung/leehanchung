# Hidden Technical Debt of AI Systems: Agent Runtime

> The original "Hidden Technical Debt in Machine Learning Systems" paper warned us about the sprawling complexity surrounding ML model code. A decade later, AI agents introduce a new category of hidden debt — the agent runtime itself.

---

## Introduction

In 2015, Sculley et al. published [Hidden Technical Debt in Machine Learning Systems](https://papers.nips.cc/paper/2015/hash/86df7dcfd896fcaf2674f757a2463eba-Abstract.html), a paper that became required reading for anyone shipping ML in production. Its central insight was that the model — the thing everyone obsesses over — is a tiny fraction of the real system. The rest is plumbing: data pipelines, serving infrastructure, monitoring, configuration, and all the glue code that holds everything together. That plumbing is where the debt accumulates.

A decade later, we are building AI agents — systems that don't just predict but *act*. They execute code, call APIs, read and write files, browse the web, and orchestrate multi-step workflows. And we are discovering that the runtime environment these agents operate in introduces a new, distinct category of hidden technical debt that the original paper never anticipated.

This article is about the **agent runtime**: what it is, why it demands sandboxing, what the current landscape looks like, and why the gap between experimentation and production is the most dangerous debt of all.

---

## What Is an Agent Runtime?

An agent runtime is the execution environment in which an AI agent carries out its actions. It is not the model. It is not the prompt. It is the infrastructure that sits between "the model decided to do X" and "X actually happened in the world."

Concretely, an agent runtime provides:

- **Code execution.** The agent writes Python, bash, SQL, or arbitrary code — something has to run it.
- **Filesystem access.** The agent reads configuration files, writes outputs, and manages state across steps.
- **Network access.** The agent calls APIs, fetches web pages, and communicates with external services.
- **Tool orchestration.** The agent invokes tools — search, databases, code interpreters, browsers — and the runtime manages their lifecycle.
- **State management.** Multi-step agentic workflows need persistent state: conversation context, intermediate results, file artifacts.
- **Resource isolation.** The agent's actions need to be bounded — in compute, memory, time, and blast radius.

If you squint, an agent runtime looks a lot like an operating system. And just like operating systems, the design choices made at the runtime layer propagate upward into every application built on top of it.

---

## Why We Need Sandboxing

The moment you give an AI agent the ability to execute arbitrary code, you are handing it a loaded weapon. Sandboxing is not optional — it is the foundational requirement.

### The Threat Model

Traditional software has a well-understood threat model: untrusted *user input* flows through trusted *code*. With AI agents, the threat model inverts. The *code itself* is untrusted — it is generated at inference time by a model that can hallucinate, misunderstand instructions, or be manipulated via prompt injection.

Consider what can go wrong when an agent executes code without proper sandboxing:

- **Data exfiltration.** The agent reads environment variables containing API keys and sends them to an external endpoint.
- **Lateral movement.** The agent discovers it is running on an EC2 instance, queries the metadata service, and obtains IAM credentials.
- **Resource exhaustion.** The agent enters an infinite loop or forks processes until the host runs out of memory.
- **Persistent compromise.** The agent writes a cron job or modifies startup scripts, establishing persistence beyond its intended session.
- **Supply chain attacks.** The agent `pip install`s a malicious package that executes arbitrary code at install time.

These are not theoretical. They are the natural consequences of running untrusted code in a trusted environment. Sandboxing is how we draw the boundary.

### What Good Sandboxing Looks Like

Effective agent sandboxing requires multiple layers:

1. **Process isolation.** The agent's code runs in a separate process tree with restricted syscalls (seccomp, AppArmor, or equivalent).
2. **Filesystem isolation.** The agent gets its own filesystem namespace. It cannot see the host filesystem, other tenants' data, or sensitive system files.
3. **Network isolation.** Egress is controlled. The agent can reach approved endpoints but cannot scan internal networks or exfiltrate data to arbitrary destinations.
4. **Resource limits.** CPU, memory, disk, and wall-clock time are capped. The agent cannot consume unbounded resources.
5. **Ephemeral by default.** The sandbox is destroyed after execution. No state persists unless explicitly captured. This limits the blast radius of any compromise.
6. **Credential scoping.** Secrets are injected narrowly. The agent gets the minimum credentials required for its task, with short TTLs and restricted permissions.

---

## The Current Landscape

### Startup Sandbox Services

A wave of startups has emerged to provide sandboxed execution environments purpose-built for AI agents:

**[Modal](https://modal.com)** provides serverless container execution with strong isolation. Each function invocation runs in its own gVisor-sandboxed container with configurable CPU, memory, GPU, and network access. Modal's model — define infrastructure in Python, get isolated execution — maps naturally to agentic workloads. Volumes provide persistent state, and the cold-start times are low enough for interactive agent loops.

**[E2B](https://e2b.dev)** (stands for "Environment to Boot") offers cloud-based sandboxed environments specifically designed for AI agents. Each sandbox is a lightweight micro-VM with its own filesystem, running processes, and network stack. E2B focuses on the code interpreter use case: give the agent a sandbox, let it execute code, and tear it down when done.

**[Fly.io Machines](https://fly.io)** provides fast-booting micro-VMs that can serve as agent sandboxes. The emphasis is on low-latency cold starts and geographic distribution, which matters for agents that need to interact with region-specific services.

**[Daytona](https://daytona.io)** focuses on development environments as code, providing sandboxed workspaces that agents can use for software development tasks. Each workspace is an isolated container with a full development toolchain.

**[Firecracker-based services](https://firecracker-microvm.github.io/)** — Several startups build on AWS's open-source Firecracker micro-VM technology to provide lightweight, fast-booting sandboxes with strong hardware-level isolation.

The common pattern: these services trade generality for speed and isolation. They boot fast, isolate strongly, and tear down cleanly.

### Cloud Provider Sandbox Services

The hyperscalers approach agent sandboxing from a different angle — bolting agent execution onto their existing compute and security primitives:

**AWS** offers multiple layers that can be composed into agent sandboxes:
- **AWS Lambda** provides function-level isolation with Firecracker micro-VMs under the hood. Each invocation gets its own sandbox with configurable memory, ephemeral storage, and VPC networking.
- **Amazon Bedrock Agents** wraps agent orchestration with built-in code interpretation, but the sandbox is opaque — you trade control for convenience.
- **ECS/Fargate** provides container-level isolation with IAM-scoped credentials, suitable for longer-running agent tasks.
- **AWS Step Functions** can orchestrate multi-step agent workflows with built-in error handling and state management.

**Azure** similarly layers agent capabilities:
- **Azure Container Apps Dynamic Sessions** provides sandboxed code interpreter sessions specifically designed for AI agents, with per-session isolation and automatic cleanup.
- **Azure Container Instances** offers fast-booting container sandboxes with VNET integration.
- **Azure AI Agent Service** integrates with Azure's identity and networking stack for enterprise agent deployments.

**Google Cloud** contributes:
- **Cloud Run** offers container-based execution with per-request isolation and scale-to-zero.
- **Vertex AI Extensions** provides managed tool execution with Google's infrastructure handling the sandboxing.

The hyperscaler pattern: strong isolation (because they already solved multi-tenancy), rich networking and IAM integration (because enterprises demand it), but often higher latency and more configuration complexity than the startups.

---

## Experimentation vs. Production: Two Different Worlds

Here is where the hidden technical debt compounds.

### What Experimentation Needs

When you are building and iterating on an agent, you need:

- **Fast feedback loops.** Change a prompt, re-run, see results in seconds. Sandbox boot time is a direct tax on iteration speed.
- **Deep observability.** Full traces of every action the agent took — every code execution, every API call, every file read/write. You need to understand *why* the agent did what it did.
- **Reproducibility.** Given the same inputs, you need to be able to replay the agent's execution. This means capturing the full environment state, not just the model outputs.
- **Permissive execution.** During development, you want the agent to be able to do more, not less. Overly restrictive sandboxing slows experimentation.
- **Cost efficiency.** You are running thousands of experimental iterations. Each one needs to be cheap — pennies, not dollars.
- **Interactive debugging.** You need to be able to pause the agent mid-execution, inspect state, modify the environment, and resume.

### What Production Needs

When the agent is serving real users or operating on real data, the requirements shift:

- **Security-first isolation.** The sandbox must assume the agent is compromised. Defense in depth, principle of least privilege, zero trust.
- **Reliability and SLAs.** The sandbox must boot, execute, and tear down reliably. Transient failures need automatic retry. Persistent failures need graceful degradation.
- **Auditability.** Every agent action must be logged, attributed, and reviewable. Compliance and governance require a full audit trail.
- **Cost predictability.** You need to understand and forecast the cost of agent execution. Runaway agents must be automatically terminated.
- **Multi-tenancy.** Multiple users' agents run on shared infrastructure. Isolation between tenants must be airtight.
- **Scalability.** Agent workloads are bursty and unpredictable. The runtime must scale from zero to thousands of concurrent sandboxes without manual intervention.

### The Gap Is the Debt

The problem is not that these requirements are different — it is that teams typically build *two completely separate systems* to meet them.

During experimentation, the agent runs on a developer's laptop, in a Jupyter notebook, or in a loosely configured cloud instance. The "sandbox" is `subprocess.run()` with a timeout. Networking is wide open. Credentials are long-lived personal tokens. State management is "I'll just write to /tmp."

Then, when it is time to go to production, everything changes. The agent moves into a container orchestration platform with network policies, IAM roles, secrets managers, and monitoring pipelines. The runtime is unrecognizable.

This gap is where bugs hide. Behaviors that worked in experimentation break in production — not because the model changed, but because the *runtime* changed. The agent relied on network access that production restricts. It assumed filesystem paths that production remaps. It expected environment variables that production scopes differently. It used libraries that production doesn't have installed.

Sound familiar? It should. This is the same category of problem that Docker solved for traditional software: the "works on my machine" gap. But for agents, the gap is wider because the agent's behavior is *emergent* — it is not written in code you can diff.

---

## Dev/Prod Parity for Agent Runtimes

The Twelve-Factor App methodology introduced the principle of [dev/prod parity](https://12factor.net/dev-prod-parity): keep development, staging, and production as similar as possible. For agent runtimes, this principle is not just good practice — it is essential.

### What Dev/Prod Parity Means for Agents

1. **Same sandbox technology.** If production runs in gVisor-sandboxed containers, development should too. If production uses Firecracker micro-VMs, your local iteration loop should use the same isolation boundary. The overhead of running a real sandbox locally is small compared to the cost of debugging production-only sandbox failures.

2. **Same filesystem layout.** The agent should see the same directory structure, the same base packages, the same system tools in dev and prod. Container images should be shared, with environment-specific configuration injected at runtime — not baked in.

3. **Same network policies.** If production restricts egress to an allowlist, development should too (with perhaps a broader allowlist, but the *mechanism* should be identical). An agent that works in development because it makes unrestricted HTTP calls will fail in production when egress is locked down.

4. **Same credential mechanisms.** Development should use the same secrets injection pattern as production — short-lived tokens, scoped permissions, injected via the same interface. The developer's personal API keys with admin access should never be the agent's credentials, even in development.

5. **Same resource limits.** If production caps the agent at 2GB of memory and 60 seconds of wall-clock time, development should enforce the same limits. An agent that passes all tests with unlimited resources will behave differently (and possibly fail catastrophically) under production constraints.

6. **Same observability stack.** The tracing, logging, and metrics pipeline should be identical. If you cannot reproduce a production trace in your development environment, you cannot debug production issues efficiently.

### How to Get There

The path to dev/prod parity for agent runtimes is not about building more infrastructure — it is about building the *right* infrastructure once and using it everywhere.

**Define the runtime as code.** The sandbox specification — base image, installed packages, resource limits, network policies, mounted volumes, injected credentials — should be a declarative artifact checked into version control. This is the agent's equivalent of a Dockerfile, but it must capture everything the agent's execution depends on.

**Run the same runtime locally.** Services like Modal already support this pattern: you define your execution environment in Python, and the same definition runs both locally (for development) and in the cloud (for production). The gap between environments shrinks to configuration differences, not infrastructure differences.

**Test against the real sandbox.** Your CI pipeline should execute agent evaluations inside the production sandbox, not in a mocked environment. If your agent evaluation suite passes in CI but fails in production, your CI is testing the wrong thing.

**Instrument the boundary.** Log every interaction between the agent and its runtime: every syscall that hits a sandbox boundary, every network request that passes through a policy filter, every resource limit that gets checked. These logs are how you detect and diagnose dev/prod drift.

---

## The Debt We Are Accumulating

The hidden technical debt of agent runtimes is accumulating right now, in every team building AI agents. It looks like:

- **"We'll add sandboxing later."** The agent prototype runs unsandboxed. It ships to production with a TODO comment. The first security incident triggers a fire drill.
- **"It works in my notebook."** The agent's behavior changes between environments because the runtime changed, but no one can pinpoint which runtime difference caused the behavior change.
- **"We'll just use Lambda/Container Apps/Cloud Run."** The team picks a production runtime without considering whether it can also serve the experimentation workflow. Two systems diverge. Bugs live in the gap.
- **"The model is the product."** The team invests heavily in prompt engineering and model selection but treats the runtime as commodity infrastructure. The runtime's constraints shape the agent's behavior more than the prompt does — but no one is measuring that.

---

## Conclusion

The original technical debt paper taught us that the model is the easy part — the system around it is where complexity and debt accumulate. For AI agents, the runtime *is* that system. It is the environment that shapes what the agent can do, how reliably it can do it, and how safely it operates.

The teams that will ship reliable agents in production are the ones that treat the runtime as a first-class concern: sandboxed from day one, consistent across environments, and observable at every layer. The teams that defer this work will rediscover, the hard way, that hidden debt always comes due.

---

*This is a draft. Feedback welcome.*
