# APM for Agentic Coding: What StarCraft, WoW, and Fortnite Teach Us About Programming with AI Agents

> In competitive gaming, being bad isn't just a personal failing — it's rude to your teammates. The same etiquette is emerging in agentic coding: if you can't manage your agents, you're wasting everyone's compute, context, and time.

---

## Don't Be the Guy Who Sucks at World of Warcraft

There's a famous essay in gaming culture titled "Why It Is Rude to Suck at Warcraft." The core argument is deceptively simple: when you show up to a raid unprepared — wrong spec, wrong gear, no knowledge of the fight mechanics — you aren't just hurting yourself. You're wasting the time of twenty-four other people who did prepare. Sucking isn't a victimless act. It is, in the context of a cooperative endeavor with shared stakes, genuinely rude.

I keep thinking about this essay as I watch programmers adopt agentic coding tools. Because the same dynamic is emerging, except the stakes are higher and the raids never end.

When you're running four coding agents in parallel across different parts of a system — one refactoring an API layer, one writing tests, one debugging a deployment pipeline, one sketching out a new feature — and you lose track of what they're doing, you don't just waste your own time. You waste tokens. You waste compute. You create merge conflicts that cost your team hours. You let an agent hallucinate an architecture decision that propagates through three pull requests before anyone catches it. In the new world of agentic coding, being bad at managing your agents is the new being bad at World of Warcraft. It's rude.

But what does it actually mean to be *good* at this? That's where things get interesting — and where competitive gaming has been training us for decades.

---

## The StarCraft Player's Brain

If you've ever watched a professional StarCraft match, you've seen something that looks superhuman. The player's camera jumps across the map every half-second. They're building workers at their main base, microing a harassment squad at the enemy's natural expansion, scouting with an observer, queuing up research, spreading creep, repositioning their army — all simultaneously, all at 300+ actions per minute.

StarCraft players talk about three fundamental resource loops that compete for their attention:

**Micro** — the tactical, moment-to-moment control of individual units. Splitting marines against banelings. Blinking stalkers back to safety. The granular, mechanical skill of making each unit do the right thing at the right time.

**Macro** — the strategic, base-level management of production. Building supply depots before you get supply-blocked. Keeping your production facilities busy. Expanding to new bases at the right timing. Never letting resources pile up unspent.

**Econ** — the economic layer that funds everything else. Worker production. Base saturation. Trade-off decisions between investing in army now versus investing in economy for a bigger army later.

The genius of StarCraft as a competitive game is that these three loops run concurrently and they all demand attention *right now*. You cannot pause macro to do micro. You cannot pause econ to do macro. The game is fundamentally about attention allocation — about developing the mental architecture to rapidly context-switch between strategic layers while maintaining coherent play across all of them.

This is exactly what agentic coding feels like.

---

## Agentic Coding Is a Real-Time Strategy Game

When you're working with multiple AI coding agents, you are playing a real-time strategy game whether you realize it or not. The three loops map almost perfectly:

**Micro** — tunnel-visioning into a single agent's conversation. Reading its output carefully. Correcting its course when it starts going off-track. Providing the precise context it needs for the current subtask. This is the equivalent of microing your units in a fight: you're right there, hands on the controls, making sure this specific engagement goes well.

**Macro** — zooming out to look at the overall architecture. How do the pieces these agents are building fit together? Are the interfaces consistent? Is one agent's work going to conflict with another's? This is production management: making sure the factory is producing the right units in the right proportions, not three overlapping implementations of the same service.

**Econ** — managing the resource layer underneath everything. Your context windows. Your token budgets. Your own cognitive bandwidth. Deciding which agents to spin up, which to pause, which to kill because they've gone down a rabbit hole that's burning resources without progress. This is the economic game: investing attention where the marginal return is highest.

The fundamental challenge is identical to StarCraft: all three loops demand attention simultaneously, and the game never pauses. While you're deep in micro with one agent — carefully steering it through a tricky database migration — your other agents are still running. They're still making decisions. They're still writing code. And if you neglect your macro for too long, you'll surface to find that two agents have built incompatible abstractions, or one has refactored a module that another is actively depending on.

The best agentic coders I've observed operate like high-APM StarCraft players. They maintain a rhythm: dive into one agent, give it thirty seconds of focused direction, pop out, scan the board, check on another agent, course-correct, zoom out to the architectural view, make sure everything still makes sense, dive back in. It's a continuous rotation of attention across micro, macro, and econ — never staying at any one level too long.

---

## The Fortnite Brain Is Different (But Also Right)

Not everyone's mental model maps to StarCraft, though. Some of the best agentic coders I've seen operate more like Fortnite players — and this is a genuinely different cognitive style, not a worse one.

Fortnite is not a multitasking game. It's a *focus* game. The skill expression in Fortnite is about tunnel vision: when you're in a fight, you are *in that fight*. Building, editing, shooting, resetting — the mechanical demands are so extreme that splitting attention means death. You don't manage an economy. You don't balance production. You drop in, you loot, and then you execute at the highest mechanical intensity you can sustain.

The Fortnite-brained agentic coder doesn't run four agents in parallel. They run one agent at a time, but they run it *hard*. They provide incredibly detailed context. They review every line of output. They iterate at blistering speed — reject, redirect, regenerate, accept, move on. When they're done with one task, they context-switch completely. Kill the agent, start a new one, tunnel vision again.

This style works because it trades breadth for depth. The StarCraft player gets more done in parallel but with looser oversight. The Fortnite player gets less done in parallel but with tighter control. Both are valid. Both are demanding. And critically, both share one thing in common: **extreme iteration speed**.

This is the deeper insight. Whether you're multitasking across agents (StarCraft) or serial-focusing through agents (Fortnite), the meta-skill is the same: *iterate as fast as possible*. Generate, evaluate, correct, regenerate. The feedback loop between you and the agent needs to be as tight as you can make it. The old dream of Extreme Programming — Kent Beck's continuous feedback loops, the idea that you should shorten every cycle time in your development process — is finally becoming real, not through discipline and process, but through the raw mechanics of human-agent interaction.

---

## The WoW Add-On Problem

Here's where World of Warcraft comes back into the picture, and where the analogy gets uncomfortably precise.

If you played WoW at any competitive level, you know that the base game is almost unplayable without add-ons. You need a damage meter to know if you're pulling your weight. You need boss mod timers to know when to move. You need a threat meter, a healing assignment tracker, a weakaura for every proc and cooldown, a custom UI layout that puts information where you actually need it. The add-on ecosystem is so essential that Blizzard effectively designs encounters around the assumption that players will have them.

This is exactly what's happening with agentic coding tooling.

Your coding agent ships with a base set of capabilities, but to actually be productive, you start bolting things on. An MCP server for database access. A CLI tool for deployment. A custom skill for PR reviews. A web search integration. A file watcher. A linter that runs automatically. Each addition makes your agent more capable — and each addition makes your setup more complex, more fragile, more specific to your workflow.

This is the **incidental add-on-driven system** that defined high-end WoW play, now manifesting in your terminal. You're not just coding anymore. You're maintaining a *loadout*. You're min-maxing your agent configuration the same way a WoW player min-maxes their gear and add-ons: which MCP servers give me the best throughput? Which custom instructions reduce the most friction? Which tool integrations save me the most context-switching?

And just like in WoW, the gap between a player with a well-configured setup and a player running stock is enormous. The min-maxed player isn't just marginally better — they're operating in a fundamentally different game. Their agent can do things that the stock agent cannot. Their feedback loops are tighter. Their iteration speed is higher. Their error rate is lower. They look like they're playing a different game because, in a meaningful sense, they are.

---

## The Code Factory and the Raid Leader

The logical endpoint of all this is what some people are calling the "code factory" — a setup where you're not writing code at all in the traditional sense. You're orchestrating agents. You're a raid leader, not a DPS player. Your job is to maintain strategic awareness, allocate resources, make calls, and keep the operation moving. The agents are your raid team.

And this is where the "rude to suck" principle becomes most acute. In a code factory setup, every mistake in your orchestration propagates. If you give an agent bad context, it writes bad code, which another agent builds on, which a third agent tests against, and now you have a beautifully tested, completely wrong implementation. The blast radius of orchestration mistakes scales with the number of agents you're running.

In WoW raiding, this was the raid leader's burden: one bad call wipes the whole group. In StarCraft, this is the cost of neglecting macro while you micro: you win the battle but lose the war because you forgot to build workers. In agentic coding, this is the cost of getting lost in one agent's conversation while three others quietly go sideways.

The skill ceiling is genuinely high. And it's high in the same ways that competitive gaming is high: it demands mechanical fluency, strategic awareness, resource management, and rapid decision-making, all simultaneously, all under time pressure, all with real consequences.

---

## The Headspace Inversion

There's one more parallel worth drawing, and it might be the most important one.

In the old model of programming — the one where you sit with a blank editor and think — the scarce resource was *headspace*. You needed quiet. You needed focus. You needed long, uninterrupted blocks of time to hold a complex system in your mind and reason about it carefully. The best programmers were the ones who could maintain the deepest mental models for the longest periods.

In the agentic model, headspace is being replaced by *action*. The scarce resource is no longer the ability to think deeply — the agents do that (or at least attempt it). The scarce resource is the ability to *act* quickly: to evaluate agent output, make decisions, redirect, approve, reject, and keep the whole system moving. You minimize thinking time and maximize action time. You minimize headspace and maximize APM.

This is the same inversion that happened in competitive gaming. Early strategy games rewarded deep thinking — chess is the extreme example. But as real-time strategy emerged, the meta shifted. Yes, you still needed strategic understanding. But the players who won were the ones who could *execute*: who could translate strategic understanding into rapid, precise action at a pace that left slower players behind.

We're watching the same shift happen in programming. The strategic understanding still matters — you still need to know architecture, design patterns, system thinking. But the execution layer has changed. The players who will win are the ones who can translate strategic understanding into rapid, precise agent orchestration.

---

## Extreme Programming, Finally

Kent Beck would be pleased, I think, though maybe also a little horrified. His vision of Extreme Programming was about tightening feedback loops: pair programming, test-driven development, continuous integration, short iterations. The principle was that faster feedback leads to better software.

Agentic coding takes this principle and pushes it past what any human process could achieve. The feedback loop between intention and implementation is now measured in seconds, not hours or days. You describe what you want, the agent produces it, you evaluate it, you redirect. The iteration cycle that used to be "write code, compile, test, debug" is now "prompt, review, correct, re-prompt."

And just like in competitive gaming, the players who thrive in this environment are the ones who've internalized the rhythm. Who don't freeze up when the feedback comes fast. Who can make good-enough decisions quickly rather than perfect decisions slowly. Who understand that in a real-time game, the cost of inaction is higher than the cost of a suboptimal action, because at least a suboptimal action generates information.

The StarCraft players, the WoW raiders, the Fortnite builders — they've been training for this. Not the specific skills, obviously, but the *meta-skill*: operating effectively under cognitive load, maintaining performance when multiple systems demand attention simultaneously, iterating at speed without sacrificing quality below the threshold that matters.

---

## So Don't Be Rude

The gaming generation is entering its prime professional years, and they're bringing with them a set of cognitive skills that the industry never explicitly valued before: high-APM multitasking, add-on ecosystem management, raid-level coordination, mechanical precision under pressure.

These skills map directly onto agentic coding. The ability to manage multiple agents is macro. The ability to steer a single agent precisely is micro. The ability to configure your tooling for maximum throughput is min-maxing your loadout. The ability to iterate at speed is your APM.

And just like in World of Warcraft, showing up unprepared is rude. If you're going to work with agents, learn to work with agents *well*. Configure your tools. Develop your rhythm. Practice your attention management. Don't be the player who shows up to the raid in quest greens with no add-ons and no knowledge of the fight.

The game is real-time now. The game has always been real-time. We just didn't have the agents to make it obvious.
