# Agent Loop with a Checklist Tool

A small, self-contained example of an **agentic loop**: a Python `while` loop that
repeatedly calls an LLM, lets it invoke tools (function calling), feeds the results
back in, and keeps going until the model produces a final answer.

The model is given two tools that let it manage a visible checklist:

- `create_checklist(descriptions)` — plan a list of steps
- `mark_complete(index, completion_notes)` — check a step off as it's completed

Output is rendered as a small terminal UI using [`rich`](https://github.com/Textualize/rich),
so you can watch the plan get created and ticked off step by step as the model works
through a problem.

This is a minimal, hand-rolled version of the pattern used by agent frameworks and
coding agents: **call model → run any requested tools → feed results back → repeat
until done.**

## How it works

1. `loop()` sends the conversation to the model along with a `tools` schema describing
   the available functions.
2. If the model's `finish_reason` comes back as `"tool_calls"`, it wants to call one or
   more tools instead of answering directly.
3. `handle_tool_calls()` looks up each requested function by name, runs it with the
   model-supplied arguments, and packages the return value as a `role: "tool"` message.
4. Both the assistant's tool-call message and the tool results are appended to the
   conversation, and the model is called again with this extended history.
5. This repeats until the model responds with plain text (`finish_reason == "stop"`),
   which is then printed.

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env   # then add your OpenRouter API key
```

Get an API key at https://openrouter.ai/.

## Run

Open `agent_loop.ipynb` in Jupyter / VS Code and run all cells. The last cell asks
the model a multi-step word problem and watches it plan and solve it using the
checklist tools.

## Extending this

- Add more tools (a calculator, web search, a file writer, ...)
- Persist checklist state across runs
- Swap the OpenRouter client for a direct OpenAI or Anthropic client
- Turn the notebook into a CLI that accepts follow-up user input after each answer

## Credit

Adapted from Ed Donner's [Agents course](https://github.com/ed-donner/agents).
