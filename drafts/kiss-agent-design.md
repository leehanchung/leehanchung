# Keep It Simple Stupid: An Agent Design Philosophy

> In 1960, Kelly Johnson told the engineers at Lockheed Skunk Works that their jets had to be repairable by an average mechanic in a field with basic tools. Sixty-five years later, the same principle applies to AI agents: if your orchestration framework is smarter than your model, you've already lost.

---

## KISS

The KISS principle — Keep It Simple Stupid — originated in the U.S. Navy and was popularized by Kelly Johnson, the legendary engineer behind the U-2 and SR-71 Blackbird at Lockheed's Skunk Works. Johnson's insight was that sophistication in the product does not require sophistication in the maintenance interface. The jets could be as advanced as physics allowed, but the tools and procedures for servicing them had to be dead simple, because in the field, under pressure, complexity kills.

This is the most important design principle for agentic systems that almost nobody is following.

---

## The Einstein on the Assembly Line

Here is what I see in the current landscape of agentic AI: teams building elaborate directed acyclic graphs to orchestrate agents. Multi-step pipelines with branching logic, retry semantics, conditional routing, state machines, and checkpoint recovery. Frameworks with thirty configuration options for controlling how an agent thinks, plans, reflects, and acts. The orchestration layer has become the product, and the model — the actual intelligence — has become a replaceable component slotted into a workflow engine.

This is like chaining Einstein to a chair on an iPhone assembly line.

You have access to a system that can reason, plan, adapt, recover from errors, and solve novel problems. And your response to having this system is to... constrain it into a rigid DAG where each node does exactly one thing and the transitions are hardcoded? You're taking a general intelligence and turning it into a function call. You're building a Rube Goldberg machine when you could just hand the problem to the smartest person in the room and say "figure it out."

The instinct to over-orchestrate comes from a reasonable place: models are imperfect, they hallucinate, they go off track, they need guardrails. All true. But the solution to "the model sometimes makes mistakes" is not "remove all autonomy from the model and replace it with software." That's throwing away the thing that makes the model valuable in the first place.

---

## The Model Is the Product

In a [talk at Data Council 2025](https://www.datacouncil.ai/talks-archive/the-model-is-the-product), I traced a pattern that repeats across every era of computing: intelligence migrates toward the center, and everything around it gets consumed.

In the PC era, the product was hardware. "Intel Inside" was the differentiator. Software was a thin layer on top. Then software ate hardware — the chip became commoditized, and the operating system and applications became the product. Microsoft didn't win because it had better silicon. It won because Windows was the intelligence layer that mattered.

Now the model is eating software. The same pattern is repeating. The surrounding infrastructure — the pipelines, the orchestration, the glue code, the workflow engines — is being absorbed into the model's capabilities. Things that used to require explicit software (routing logic, error handling, format conversion, data validation) are increasingly things the model can just *do*, if you let it.

This has a profound implication for agent design: **the complexity you build around the model today is the complexity the model will make obsolete tomorrow.**

Every rigid orchestration layer you build is a bet against model improvement. Every hardcoded DAG is a bet that the model will never be smart enough to figure out the sequencing on its own. Every elaborate state machine is a bet that planning is too hard for the model and must be done in software. These are bad bets. Models are getting smarter on a trajectory that shows no signs of slowing — capability is scaling with compute, and semiconductor scaling continues to deliver.

If you build your agent system around the assumption that the model is dumb and needs to be micromanaged by software, you will be perpetually rebuilding your system as models get smarter. You'll be ripping out orchestration logic that the model has outgrown, replacing routing heuristics with direct model calls, and wondering why your "simple wrapper" has become legacy code in six months.

If instead you build your agent system around the assumption that the model is smart and getting smarter, you build something that *improves automatically* as models improve. Your system gets better without you touching it. That's the right side of the bet.

---

## Keep Tools Simple Stupid

The KISS principle applies with particular force to tool design. Tools are how agents interact with the world — they read files, query databases, call APIs, run commands. And there's a strong temptation to make tools "smart": tools that validate their own inputs, tools that interpret ambiguous requests, tools that have twenty configuration options and three modes of operation.

Resist this temptation. Tools should be stupid. Tools should be passthroughs.

Think about it this way: do you need a smart cup holder? One with WiFi and Bluetooth and an app that tracks your hydration and adjusts the temperature based on the weather forecast and your calendar? Or do you need a cup holder that holds cups?

You need a cup holder that holds cups.

The Internet of Things gave us a preview of what happens when you make simple interfaces complex: smart fridges that need firmware updates and crash during dinner, Bluetooth-enabled shower heads that lose pairing and spray cold water, connected toasters with security vulnerabilities. The entire Internet of Shit — the derisive term the industry earned — happened because engineers couldn't resist adding intelligence to things that didn't need it.

Agent tools are heading down the same path. I see tools with elaborate input schemas, tools that try to "understand" what the agent wants before executing, tools that pre-validate and post-process and transform and cache. Each layer of smartness is a layer that can break, a layer that can conflict with the model's reasoning, a layer that makes debugging harder, and a layer that will be unnecessary once the model gets slightly smarter.

A good tool does one thing. It takes an input, performs an action, returns a result. It doesn't interpret. It doesn't optimize. It doesn't second-guess. It's a controlled, predictable interface between the agent and the world. The intelligence lives in the model. The tool is just the hand.

---

## The Agent Harness Should Be Boring

Extending KISS from tools to the overall harness: the agent harness — the thing that manages the conversation loop, handles tool calls, maintains context — should be the most boring piece of software in your stack.

It should be a loop. A simple loop. The model generates a response. If the response contains a tool call, execute the tool, feed the result back. If not, present the response. Handle errors. Manage context length. That's it.

The moment your harness starts making decisions — routing between models, selecting tools on behalf of the agent, pruning context based on heuristics, implementing reflection loops in application code — you've moved intelligence out of the model and into software. And software intelligence doesn't improve with the next model release. It's frozen. It's technical debt.

The boring harness has a superpower: it gets better automatically. When a smarter model drops, the boring harness benefits immediately because all the intelligence is in the model and the harness doesn't constrain it. The clever harness, meanwhile, is fighting the new model's capabilities — the routing logic conflicts with the model's improved planning, the context pruning removes information the smarter model would have used, the hardcoded reflection steps waste tokens on self-critique the model no longer needs.

Boring is a feature. Boring scales with model intelligence. Clever scales with engineering effort.

---

## What You Actually Need

So what does a KISS-compliant agent system look like? It's shorter than you'd think:

**A model.** The best one you can afford. This is where your budget goes. Not into orchestration engineering — into model capability.

**A loop.** Prompt in, response out, tool calls handled. Error boundaries at the edges. Context management when you hit limits. Nothing else.

**Simple tools.** Each one does exactly one thing. File read. File write. Shell command. HTTP request. Database query. No interpretation layers. No smart wrappers. The model decides what to call and how to use the result.

**Guardrails at the boundary, not in the middle.** Validate what goes out to the world (don't execute rm -rf /, don't send emails without confirmation). Don't validate what happens inside the agent's reasoning. Let it think however it thinks.

That's it. Four components. Everything else is accidental complexity that you're adding because you don't trust the model — and the model is getting more trustworthy faster than your orchestration code is getting better.

---

## But What About Reliability?

The immediate objection: "Simple systems aren't reliable. We need retries, fallbacks, error handling, monitoring."

Yes. You need all of those things. But none of those things require a smart orchestration layer. Retries are a loop. Fallbacks are a conditional. Error handling is a try-catch. Monitoring is logging. These are infrastructure concerns, not intelligence concerns. They belong in the harness the same way TCP/IP belongs in the kernel — as plumbing, not as product.

The mistake is conflating *reliability engineering* with *intelligence engineering*. You can have a perfectly reliable system that's also perfectly simple. The Linux kernel is one of the most reliable pieces of software ever written, and its design philosophy is aggressively simple. Reliability comes from simplicity, not from complexity. Every additional layer of orchestration is an additional failure mode.

---

## The Semiconductor Bet

The deepest reason to keep agent systems simple is that you're making a bet on the future, and the bet has asymmetric payoffs.

If you bet that models will stay roughly as capable as they are today, then yes, you need complex orchestration to compensate for their limitations. Build your DAGs, your state machines, your elaborate multi-agent workflows with hand-tuned routing.

But if you bet that models will continue to improve — and every trend in semiconductor scaling, training methodology, and research investment says they will — then every piece of orchestration complexity you build today is waste. It's code you'll have to maintain until you rip it out. It's abstractions that will fight against improved model capabilities. It's engineering effort invested in the wrong layer of the stack.

The model is the product. The model is getting better. Build the thinnest possible layer around it and get out of the way.

Kelly Johnson knew this in the 1960s. The jet is the product. The tools are for the mechanic. Keep them simple, because the jet is going to keep getting faster, and the last thing you want is a maintenance procedure that can't keep up.

---

## Keep It Simple Stupid

The KISS principle isn't about laziness or corner-cutting. It's about respecting where the intelligence actually lives and not duplicating it in the wrong layer. In agentic AI, the intelligence lives in the model. Let it.

Build simple harnesses. Build stupid tools. Build boring infrastructure. Bet on the model. The model is the product, and it's getting better every quarter. Everything else is a cup holder — and nobody needs a cup holder with WiFi.
