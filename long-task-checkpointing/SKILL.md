---
name: long-task-checkpointing
description: "Run long AI-agent tasks so they survive context limits, crashes, and machine restarts: checkpoint state to files and resume from disk instead of memory. Use for any agent job longer than one session."
---

# Long-Task Checkpointing

Agent sessions end: context fills, processes die, machines get replaced. Design every long job as if the current session will vanish mid-sentence — because eventually one will.

## The checkpoint file

One file per job, updated at every meaningful step, containing:

- Goal and definition of done (one or two lines)
- Current state: what is finished, what is in flight, what is blocked and on whom
- Decisions made so far, with the reason in one line each
- Exact next action (specific enough that a fresh agent with zero memory can start)
- Pointers: file paths, URLs, IDs — anything the next session would otherwise have to rediscover

## Rules

- Write the checkpoint before starting any step that takes more than a few minutes, and after finishing it
- Never keep the only copy of a decision in the conversation; if it matters, it goes in the file
- On resume, read the checkpoint first and trust it over your recollection of the conversation
- Keep a separate append-only log for evidence and outputs; the checkpoint stays short enough to read in one screen
- Anything that must survive (briefs, drafts, ledgers) lives in files from the start, not as attachments to a chat

## Human gates

Mark steps that need a human (payments, approvals, 2FA, anything irreversible) as explicit stop points in the checkpoint. The agent prepares everything up to the gate, stops, and reports what is needed — it never routes around a gate.

## The resume test

Before ending any session, ask: could a brand-new agent, given only the checkpoint file, continue this job without asking me anything? If not, the checkpoint is not done.
