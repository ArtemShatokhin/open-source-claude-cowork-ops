# Open-Source Audit and Change Requests in Kortix

[Kortix](https://kortix.com) is the open-source AI Management System, and it
puts two controls between an agent and your systems: a complete audit trail of
what the agent did, and a change request a person reads before any of it
reaches `main`. An agent can open a change request. An agent cannot merge one.

## Every governed action is recorded

The control is enforced in the platform, below the model, so a prompt cannot
argue its way past the record. Each connector call is written to a centralized
log with the fields a security review asks for:

| Field | Example |
| --- | --- |
| Connector and action | `gmail.send_email` |
| Actor | The agent and the person or trigger behind the session |
| Outcome | ran, waiting on approval, denied or errored |
| Effect | read, write or destroy |
| Arguments | A hash, never the raw values |

A session can be reconstructed in order, account and project logs list newest
first, and an export reaches back 365 days. Audit webhooks stream the same
events to a SIEM, and reading account-wide audit data needs the enterprise
entitlement. Because an agent acts as its own principal, every event records
both the agent and the person who started it.

## Review gate on the way to main

Each session runs in an isolated machine on a branch named after the session.
The agent commits and pushes to that branch. When the work is ready it opens a
change request, and the project's default branch stays untouched until a
person merges it. That merge is deny-by-default for an agent, so the company
improves one reviewed change at a time.

The three commands that get a project running:

```sh
curl -fsSL https://kortix.com/install | bash
kortix init
kortix ship
```

List what is waiting for a human:

```sh
kortix cr ls
```

The review inbox holds more than code. A change request carries a summary and
a unified diff. A connector call a policy held for approval appears with its
arguments. An output, a decision or a batch an agent submits for sign-off
appears the same way. A person approves, rejects, or sends the item back to
the agent with a note, and an agent may never resolve its own approval.

See [Read the docs](https://kortix.com/docs) for the full command reference;
the source is [Kortix on GitHub](https://github.com/kortix-ai/suna). If you are
still choosing an alternative, the [open-source Claude Cowork
comparison](https://opensourceclaudecowork.com) puts the self-hostable options
side by side.
