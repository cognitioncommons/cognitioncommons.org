---
title: "Tool-Using Agent Architectures"
date: 2024-12-26
category: architecture
status: published
---

# Tool-Using Agent Architectures

Patterns for building agents that effectively use external tools to accomplish tasks beyond pure language modeling.

## Why Tools Matter

Language models alone are limited:
- No access to current information
- Cannot execute code or verify results
- Cannot interact with external systems
- Knowledge frozen at training cutoff

Tools extend capabilities:
- Web search for current information
- Code execution for computation
- APIs for real-world actions
- Databases for structured queries

## Core Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Agent Core                               │
│                     (Planning + Reasoning)                       │
└───────────────────────────┬─────────────────────────────────────┘
                            │
              ┌─────────────┼─────────────────┐
              │             │                 │
              ▼             ▼                 ▼
        ┌──────────┐  ┌──────────┐     ┌──────────┐
        │  Search  │  │  Code    │     │  API     │
        │  Tool    │  │  Exec    │     │  Calls   │
        └──────────┘  └──────────┘     └──────────┘
```

The agent decides when to use tools, how to use them, and how to interpret results.

## Tool Categories

### Information Retrieval
- Web search
- Document search
- Knowledge base queries
- Database lookups

### Computation
- Code execution (sandboxed)
- Mathematical computation
- Data analysis
- Simulations

### External Actions
- API calls
- File operations
- Message sending
- System commands

### Verification
- Fact checking
- Code testing
- Output validation

## Design Patterns

### 1. ReAct (Reasoning + Acting)

Interleave thinking and tool use:

```
Thought: I need to find the current price
Action: search("AAPL stock price")
Observation: $185.23 as of today
Thought: Now I can answer the question
Response: The current Apple stock price is $185.23
```

**Strengths:** Interpretable, flexible
**Weaknesses:** Can get stuck in loops

### 2. Plan-Then-Execute

Create full plan before any action:

```
Plan:
1. Search for X
2. Extract data from results
3. Compute Y
4. Format response

Execute:
[Run each step sequentially]
```

**Strengths:** Efficient, fewer API calls
**Weaknesses:** Can't adapt mid-execution

### 3. Tree of Thoughts

Explore multiple paths, backtrack if needed:

```
        ┌─ Path A (search first)
Query ──┼─ Path B (compute first)
        └─ Path C (ask clarification)

Evaluate paths, pursue best one
```

**Strengths:** Handles uncertainty
**Weaknesses:** Computationally expensive

### 4. Hierarchical Agents

Delegate to specialized sub-agents:

```
Main Agent
    ├─ Research Agent (search, summarize)
    ├─ Code Agent (write, execute, debug)
    └─ Communication Agent (format, explain)
```

**Strengths:** Separation of concerns
**Weaknesses:** Coordination overhead

## Tool Selection

How does an agent decide which tool to use?

### Capability Matching

Map task requirements to tool capabilities:

| Task Needs | Tool |
|------------|------|
| Current information | Web search |
| Computation | Code execution |
| Structured data | Database query |
| External action | API call |

### Learned Selection

Train on examples of successful tool use:
- Fine-tune on tool-use trajectories
- Reinforce successful tool selections
- Learn from failures

### Explicit Reasoning

Prompt agent to reason about tool choice:

```
Available tools: [search, code, calculator]
Task: Calculate compound interest over 10 years
Reasoning: This requires mathematical computation.
Calculator could work but code is more flexible.
Selected tool: code
```

## Error Handling

Tools fail. Agents must handle failures gracefully:

### Retry with Modification

```
Action: search("obscure query")
Observation: No results found
Action: search("broader query")  # Modified query
```

### Fallback Tools

```
Action: api_call(endpoint)
Observation: API rate limited
Action: search("same information")  # Fallback to search
```

### Graceful Degradation

```
Action: verify_fact(claim)
Observation: Verification service unavailable
Response: [Provide answer with caveat about unverified claim]
```

## Security Considerations

Tool use introduces risks:

### Sandboxing

- Execute code in isolated environments
- Limit file system access
- Restrict network calls
- Cap resource usage

### Input Validation

- Sanitize tool inputs
- Prevent injection attacks
- Validate against schemas

### Output Filtering

- Check tool outputs for sensitive data
- Filter before including in responses
- Log for audit

### Permission Scoping

- Minimum necessary permissions
- User-specific access controls
- Action confirmation for risky operations

## Evaluation

Measuring tool-using agent quality:

### Task Completion

- Does the agent accomplish the goal?
- How many steps required?
- What's the error rate?

### Tool Efficiency

- Appropriate tool selection?
- Minimal unnecessary tool calls?
- Effective use of tool outputs?

### Robustness

- Handles tool failures gracefully?
- Recovers from errors?
- Works with noisy tool outputs?

## Implementation Considerations

### Latency

Tool calls add latency:
- Parallelize independent tool calls
- Cache repeated queries
- Timeout handling

### Cost

Tool calls may have costs:
- API rate limits
- Compute costs for code execution
- Search API charges

### Observability

Track tool usage:
- Log all tool calls and results
- Monitor for failures
- Trace reasoning chains

## Open Questions

1. How to teach agents optimal tool selection?
2. Can agents learn to use new tools without retraining?
3. How to compose tools for complex workflows?
4. What's the right level of autonomy for tool use?

## References

- ReAct: Synergizing Reasoning and Acting (Yao et al.)
- Toolformer (Schick et al.)
- LangChain and agent framework documentation
- Function calling in modern LLM APIs
