
## Current state

<svg viewBox="0 0 1120 208" role="img" aria-label="Trace of one turn on the current subagent path: four serial model calls, with the first answer token arriving only at the orchestrator re-narration.">
  <defs><marker id="tickA" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto"><polygon points="0,0 6,3 0,6" fill="currentColor"></polygon></marker></defs>
  <text x="280" y="14" font-family="Arial, Helvetica, sans-serif" font-size="10.5" fill="#7A7A7A" text-anchor="middle">turn start</text>
  <line x1="280" y1="24" x2="1029" y2="24" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="280" y1="20" x2="280" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="467" y1="20" x2="467" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="655" y1="20" x2="655" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="842" y1="20" x2="842" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="1029" y1="20" x2="1029" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <rect x="274.0" y="48" width="764.8" height="144" fill="rgba(128,128,128,0.12)"></rect>
  <text x="260" y="61.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">transport + session + state</text>
  <rect x="280.0" y="51.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="79.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 1 &middot; router emits Task()</text>
  <rect x="281.4" y="69.5" width="112.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="97.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">set up the subagent</text>
  <rect x="393.4" y="87.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="115.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 2 &middot; subagent picks tool</text>
  <rect x="394.8" y="105.5" width="140.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="133.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">domain tool + HTTP</text>
  <rect x="534.8" y="123.5" width="70.0" height="11" rx="2" fill="#A0A0A0"></rect>
  <text x="260" y="151.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 3 &middot; composes return string</text>
  <rect x="604.8" y="141.5" width="252.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="169.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 4 &middot; re-narration</text>
  <rect x="856.8" y="159.5" width="168.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="187.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">replay steps + persist</text>
  <rect x="1024.8" y="177.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <line x1="856.8" y1="48" x2="856.8" y2="196" stroke="currentColor" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#tickA)"></line>
  <text x="856.8" y="44" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="middle">first answer token</text>
</svg>

**Impl. styles**
- Orchestrator mediates and re-narrates

**Tradeoffs**
- Full orchestrator authority: sequencing, validation, misroute recovery
- Handles tasks spanning several agents natively, at roughly seven calls for a two-capability turn
- Voice and policy applies centrally. Response formatting rules written once, in the orchestrator prompt
- HITL broken by turns.  
- Highest call count, latest answer
- Same answer decoded twice
- Coordination cost by a single agent
- Context isolation but partial: the subagent's trace stays out, but its return string enters the
  orchestrator's context as a tool response on every turn
- Nothing visible until the end

## Subagent streaming

<svg viewBox="0 0 1120 208" role="img" aria-label="Trace of one turn where the subagent answers the user directly: three serial model calls, with the orchestrator interception shown as a forfeited dashed span.">
  <defs><marker id="tickB" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto"><polygon points="0,0 6,3 0,6" fill="currentColor"></polygon></marker></defs>
  <text x="280" y="14" font-family="Arial, Helvetica, sans-serif" font-size="10.5" fill="#7A7A7A" text-anchor="middle">turn start</text>
  <line x1="280" y1="24" x2="1029" y2="24" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="280" y1="20" x2="280" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="467" y1="20" x2="467" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="655" y1="20" x2="655" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="842" y1="20" x2="842" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="1029" y1="20" x2="1029" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <rect x="274.0" y="48" width="756.8" height="144" fill="rgba(128,128,128,0.12)"></rect>
  <text x="260" y="61.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">transport + session + state</text>
  <rect x="280.0" y="51.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="79.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 1 &middot; router emits Task()</text>
  <rect x="281.4" y="69.5" width="112.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="97.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">set up the subagent</text>
  <rect x="393.4" y="87.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="115.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 2 &middot; subagent picks tool</text>
  <rect x="394.8" y="105.5" width="140.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="133.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">domain tool + HTTP</text>
  <rect x="534.8" y="123.5" width="70.0" height="11" rx="2" fill="#A0A0A0"></rect>
  <text x="260" y="151.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 3 &middot; answers, terminal</text>
  <rect x="604.8" y="141.5" width="252.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="169.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">orchestrator interception</text>
  <rect x="856.8" y="159.5" width="168.0" height="11" rx="2" fill="none" stroke="currentColor" stroke-width="1.2" stroke-dasharray="4 3"></rect>
  <text x="1032.8" y="169.0" font-family="Arial, Helvetica, sans-serif" font-size="10" fill="currentColor">forfeited</text>
  <text x="260" y="187.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">persist</text>
  <rect x="856.8" y="177.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <line x1="604.8" y1="48" x2="604.8" y2="196" stroke="currentColor" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#tickB)"></line>
  <text x="604.8" y="44" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="middle">first answer token</text>
</svg>

## Keeps the orchestrator as author

**Impl. styles**
- Stream subagent activity only (tool calls, step events)
- Orchestrator starts streaming (inner thought) before dispatch
- Cheaper, shorter router

**Tradeoffs**
- Perceived latency improves
- No contract or prompt changes needed to improve percieved latency
- Overall turn around not changed 
- Answer still waits on the orchestrator's re-narration
- Re-entry (HITL) still inferred rather than enforced

## Moves authoring to the subagent, orchestrator keeps control

**Impl. styles**
- Structured response to orchestrator with pass-through response
- Deferred final response (optimistic stream, orchestrator veto)
- Optimistic pass-through with exception fallback

**Tradeoffs**
- Answer arrives earlier; re-narration disappears
- Orchestrator still decides termination, but in code rather than a model call
- Strongest turn-boundary HITL: the structured envelope carries the resume signal, so re-entry is a code decision rather than an inference
- Context isolation becomes real: the subagent's context never writes back, so the orchestrator's own context stays small no matter how much work the agent does
- Tasks spanning several agents still work, since the orchestrator can dispatch again, but the seam shows because the first agent's text is already on screen
- Requires a contract with every subagent, which drifts as capabilities evolve
- Text is already on screen if the orchestrator then continues or rejects
- Voice and conduct rules move into each subagent


## Moves authority to the subagent

**Impl. styles**
- returnDirect / one-shot delegation
- Handoff chain (subagent transfers to peer)

**Tradeoffs**
- Earliest answer and fewest calls of the authored options
- Strongest context isolation
- Handoff spans agents in about five calls against the orchestrator's seven
- Fits best when agents are self-contained and nearly every request is served by one agent, with handoff kept for rare edges such as post-requisition to candidate matching
- Orchestrator loses termination authority: no sequencing, validation, or misroute recovery
- Misroutes surface raw to the user
- Degrades if requests routinely span agents, since routing knowledge then spreads across every peer
- Response formatting rules have to be repeated in every subagent prompt

## HITL from subagent

**Impl. styles**
- Subagent yield and resume 

**Tradeoffs**
- In-turn HITL for subagents
- Unlocks several pauses inside one logical step, and resuming a partial tool sequence within the same turn
- No longer on the critical path now that turn-boundary questioning covers the interactive requirement

## Main agent invoke tools (direct)

<svg viewBox="0 0 1120 154" role="img" aria-label="Trace of one turn with the capability module bound in code from session state: two serial model calls and no selection round trip.">
  <defs><marker id="tickC" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto"><polygon points="0,0 6,3 0,6" fill="currentColor"></polygon></marker></defs>
  <text x="280" y="14" font-family="Arial, Helvetica, sans-serif" font-size="10.5" fill="#7A7A7A" text-anchor="middle">turn start</text>
  <line x1="280" y1="24" x2="1029" y2="24" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="280" y1="20" x2="280" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="467" y1="20" x2="467" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="655" y1="20" x2="655" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="842" y1="20" x2="842" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="1029" y1="20" x2="1029" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <rect x="274.0" y="48" width="483.4" height="90" fill="rgba(128,128,128,0.12)"></rect>
  <text x="260" y="61.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">transport + session + state + module</text>
  <rect x="280.0" y="51.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="79.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 1 &middot; agent picks tool</text>
  <rect x="281.4" y="69.5" width="140.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="97.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">domain tool + HTTP</text>
  <rect x="421.4" y="87.5" width="70.0" height="11" rx="2" fill="#A0A0A0"></rect>
  <text x="260" y="115.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 2 &middot; answer, streaming</text>
  <rect x="491.4" y="105.5" width="252.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="133.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">next steps + persist</text>
  <rect x="743.4" y="123.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <line x1="491.4" y1="48" x2="491.4" y2="142" stroke="currentColor" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#tickC)"></line>
  <text x="491.4" y="44" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="middle">first answer token</text>
</svg>

**Impl. styles**
- Module bound from workflow state, such as an in-progress draft status
- Module bound from request context: a requisition id, a UI action, a deep link
- Module bound from the role or persona claims on the token
- Single prompt without modules

**Tradeoffs**
- Fewest model calls 
- Earliest first answer token
- Only the module's tools bind
- Tools are hard-scoped per request
- Streaming and mid-flow questioning work natively, as in any foreground design
- Response formatting rules are written once
- Modularity weakens: a capability stops being a self-contained unit
- Adding a capability means touching binding code, not just dropping in a file
- Couples the front end to the routing decision, since the UI has to pass capability context
- The whole prompt gets cached, so biggest latency and cost advantage

## Skills invoke tools

<svg viewBox="0 0 1120 190" role="img" aria-label="Trace of one turn where the agent selects a skill module: three serial model calls on the first turn of a capability.">
  <defs><marker id="tickD" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto"><polygon points="0,0 6,3 0,6" fill="currentColor"></polygon></marker></defs>
  <text x="280" y="14" font-family="Arial, Helvetica, sans-serif" font-size="10.5" fill="#7A7A7A" text-anchor="middle">turn start</text>
  <line x1="280" y1="24" x2="1029" y2="24" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="280" y1="20" x2="280" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="467" y1="20" x2="467" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="655" y1="20" x2="655" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="842" y1="20" x2="842" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="1029" y1="20" x2="1029" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <rect x="274.0" y="48" width="596.8" height="126" fill="rgba(128,128,128,0.12)"></rect>
  <text x="260" y="61.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">transport + session + state</text>
  <rect x="280.0" y="51.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="79.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 1 &middot; agent picks the skill</text>
  <rect x="281.4" y="69.5" width="112.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="97.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">read SKILL.md</text>
  <rect x="393.4" y="87.5" width="8.0" height="11" rx="2" fill="none" stroke="#A0A0A0" stroke-width="1.2"></rect>
  <text x="260" y="115.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 2 &middot; agent picks tool</text>
  <rect x="394.8" y="105.5" width="140.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="133.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">domain tool + HTTP</text>
  <rect x="534.8" y="123.5" width="70.0" height="11" rx="2" fill="#A0A0A0"></rect>
  <text x="260" y="151.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 3 &middot; answer, streaming</text>
  <rect x="604.8" y="141.5" width="252.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="169.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">next steps + persist</text>
  <rect x="856.8" y="159.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <line x1="604.8" y1="48" x2="604.8" y2="178" stroke="currentColor" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#tickD)"></line>
  <text x="604.8" y="44" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="middle">first answer token</text>
</svg>

**Impl. styles**
- Model-selected loading, where the agent invokes the skill it judges relevant
- A procedure skill that loads a knowledge skill mid-flow, immediately before it is needed
- Shared knowledge skills carrying voice, conduct and response formatting

**Tradeoffs**
- Two model calls on a warm turn against four
- Streaming and mid-flow questioning work natively
- Tasks spanning several agents need no handoff
- Voice, conduct and response formatting live in one shared knowledge skill
- Context pollution is the central risk
- Compaction is promoted from incidental to mandatory
- A loaded body never unloads, so switching task leaves the previous skill still instructing, alongside stale workspace state and its tool results
- Detecting an intent switch is easy in one context, isolating it is not
- Tools are soft-scoped only
- Only the static header caches across sessions. The body lands at a conversation-dependent offset among tool results, so it never joins the shared prefix
- Load order varies by conversation, so two sessions doing identical work diverge, leaving no prefix to cache against
- All tool schemas bind on every turn regardless of the capability in use, making the static portion larger than a scoped module's
- A loaded body is a recurring per-turn cost for the rest of the session, so bodies must stay short and split
- A misbehaving skill runs in the main loop rather than a contained one
- Attribution gets harder: with several bodies resident, tracing a behaviour to one skill is guesswork
- Granularity is a must

## Skills invoke tools (warm)

<svg viewBox="0 0 1120 154" role="img" aria-label="Trace of one turn where a skill body is already resident from an earlier turn: two serial model calls.">
  <defs><marker id="tickE" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto"><polygon points="0,0 6,3 0,6" fill="currentColor"></polygon></marker></defs>
  <text x="280" y="14" font-family="Arial, Helvetica, sans-serif" font-size="10.5" fill="#7A7A7A" text-anchor="middle">turn start</text>
  <line x1="280" y1="24" x2="1029" y2="24" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="280" y1="20" x2="280" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="467" y1="20" x2="467" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="655" y1="20" x2="655" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="842" y1="20" x2="842" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <line x1="1029" y1="20" x2="1029" y2="28" stroke="#B4B4B4" stroke-width="1"></line>
  <rect x="274.0" y="48" width="483.4" height="90" fill="rgba(128,128,128,0.12)"></rect>
  <text x="260" y="61.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">transport + session + state</text>
  <rect x="280.0" y="51.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <text x="260" y="79.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 1 &middot; agent picks tool</text>
  <rect x="281.4" y="69.5" width="140.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="97.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">domain tool + HTTP</text>
  <rect x="421.4" y="87.5" width="70.0" height="11" rx="2" fill="#A0A0A0"></rect>
  <text x="260" y="115.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">MODEL 2 &middot; answer, streaming</text>
  <rect x="491.4" y="105.5" width="252.0" height="11" rx="2" fill="#7C7C7C"></rect>
  <text x="260" y="133.0" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="end">next steps + persist</text>
  <rect x="743.4" y="123.5" width="8.0" height="11" rx="2" fill="none" stroke="#8A8A8A" stroke-width="1.2"></rect>
  <line x1="491.4" y1="48" x2="491.4" y2="142" stroke="currentColor" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#tickE)"></line>
  <text x="491.4" y="44" font-family="Arial, Helvetica, sans-serif" font-size="11" fill="currentColor" text-anchor="middle">first answer token</text>
</svg>
