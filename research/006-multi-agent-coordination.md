---
title: "Multi-Agent Coordination"
date: 2024-12-26
category: coordination
status: published
---

# Multi-Agent Coordination

Patterns for multiple AI agents working together on complex tasks.

## Why Multiple Agents?

Single-agent systems have limitations:
- Context window constraints
- Jack-of-all-trades, master of none
- No checks on individual reasoning
- Sequential processing bottleneck

Multi-agent systems can:
- Distribute work across specialists
- Provide checks and balances
- Process in parallel
- Scale to larger problems

## Coordination Patterns

### 1. Orchestrator-Worker

A central coordinator dispatches tasks to specialized workers:

```
                    ┌─────────────────┐
                    │  Orchestrator   │
                    └────────┬────────┘
           ┌─────────────────┼─────────────────┐
           ▼                 ▼                 ▼
     ┌──────────┐      ┌──────────┐      ┌──────────┐
     │ Research │      │  Code    │      │  Review  │
     │  Agent   │      │  Agent   │      │  Agent   │
     └──────────┘      └──────────┘      └──────────┘
```

**How it works:**
- Orchestrator breaks down task
- Assigns subtasks to appropriate workers
- Aggregates results
- Handles failures and retries

**Use when:** Tasks decompose into independent subtasks.

### 2. Pipeline

Agents process sequentially, each building on the previous:

```
Input → [Research] → [Draft] → [Review] → [Edit] → Output
```

**How it works:**
- Each agent has specialized role
- Output of one feeds input of next
- Quality gates between stages

**Use when:** Tasks have natural stages.

### 3. Debate / Adversarial

Agents critique each other to improve outputs:

```
┌─────────────┐         ┌─────────────┐
│  Proposer   │◄───────►│   Critic    │
└─────────────┘         └─────────────┘
        │                       │
        └───────────┬───────────┘
                    ▼
              ┌───────────┐
              │   Judge   │
              └───────────┘
```

**How it works:**
- One agent proposes solution
- Another critiques it
- Iterate until consensus or timeout
- Optional judge makes final call

**Use when:** Quality matters more than speed, need to catch errors.

### 4. Ensemble / Voting

Multiple agents tackle same problem, aggregate results:

```
            Query
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌───────┐ ┌───────┐ ┌───────┐
│Agent A│ │Agent B│ │Agent C│
└───┬───┘ └───┬───┘ └───┬───┘
    │         │         │
    └─────────┼─────────┘
              ▼
        ┌───────────┐
        │ Aggregate │
        └───────────┘
```

**How it works:**
- Same query sent to multiple agents
- Results combined (voting, averaging, selection)
- Reduces variance and catches outliers

**Use when:** Reliability critical, agents may have different strengths.

### 5. Hierarchical

Nested structure with managers and workers:

```
                ┌─────────────┐
                │   Manager   │
                └──────┬──────┘
         ┌─────────────┴─────────────┐
         ▼                           ▼
    ┌─────────┐                 ┌─────────┐
    │Team Lead│                 │Team Lead│
    └────┬────┘                 └────┬────┘
    ┌────┴────┐                 ┌────┴────┐
    ▼         ▼                 ▼         ▼
┌───────┐ ┌───────┐         ┌───────┐ ┌───────┐
│Worker │ │Worker │         │Worker │ │Worker │
└───────┘ └───────┘         └───────┘ └───────┘
```

**How it works:**
- High-level manager delegates to team leads
- Team leads coordinate their workers
- Results flow back up

**Use when:** Large complex tasks requiring many agents.

## Communication Protocols

### Message Passing

Agents communicate via structured messages:

```json
{
  "from": "research_agent",
  "to": "writer_agent",
  "type": "handoff",
  "content": {
    "findings": [...],
    "sources": [...]
  }
}
```

### Shared State

Agents read/write to common workspace:

```
┌─────────────────────────────────┐
│        Shared Workspace         │
│  - documents                    │
│  - status                       │
│  - intermediate results         │
└─────────────────────────────────┘
         ▲           ▲
         │           │
    ┌────┴───┐  ┌────┴───┐
    │Agent A │  │Agent B │
    └────────┘  └────────┘
```

### Event-Driven

Agents react to events in the system:

- Agent completes task → trigger dependent agents
- Error occurs → trigger error handler
- Timeout → trigger escalation

## Specialization Strategies

### By Skill

Different agents for different capabilities:
- Research agent: Good at search and summarization
- Code agent: Good at writing and debugging code
- Review agent: Good at finding errors

### By Domain

Different agents for different knowledge areas:
- Legal agent: Trained on legal documents
- Medical agent: Trained on medical literature
- Technical agent: Trained on technical docs

### By Role

Different agents for different functions:
- Planner: Creates execution plans
- Executor: Carries out plans
- Critic: Reviews outputs

## Failure Handling

Multi-agent systems have more failure modes:

### Agent Failure

Single agent fails:
- Retry with same agent
- Fallback to different agent
- Escalate to human

### Communication Failure

Messages lost or corrupted:
- Acknowledgment protocols
- Retry with exponential backoff
- Timeout and fallback

### Coordination Failure

Agents get stuck or loop:
- Global timeout
- Deadlock detection
- Progress monitoring

### Cascade Failure

One failure triggers others:
- Isolation between agents
- Circuit breakers
- Graceful degradation

## Practical Considerations

### Cost Management

Multiple agents multiply costs:
- Use smaller models for simpler tasks
- Cache and reuse results
- Batch related requests

### Latency

Coordination adds latency:
- Parallelize where possible
- Minimize round trips
- Async processing for non-blocking tasks

### Observability

Harder to debug multi-agent systems:
- Log all inter-agent communication
- Trace requests through system
- Monitor agent health

### Testing

More complex testing requirements:
- Unit test individual agents
- Integration test agent interactions
- End-to-end test full workflows
- Chaos testing for failure modes

## Open Questions

1. How to automatically determine optimal agent topology?
2. Can agents learn to coordinate without explicit protocols?
3. How to balance specialization vs generalization?
4. What are the limits of multi-agent scaling?

## References

- Multi-agent reinforcement learning literature
- Distributed systems coordination patterns
- LangGraph, AutoGen, CrewAI frameworks
- Agent communication languages (historical)
