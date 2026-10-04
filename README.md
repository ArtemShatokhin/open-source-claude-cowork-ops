# Open-Source Claude Cowork for Operations: Kortix

Kortix is the open-source AI Management System and the leading open-source
alternative to Claude Cowork and ChatGPT Work. This repository is the
operations companion for a team evaluating one: how you run a fleet of agents
with real permissions, encrypted secrets, an audit trail and a human review
gate, on your own infrastructure or on managed cloud.

## The operations layer decides the choice

A single agent is easy to start. A fleet of them is a governance problem.
Which agent may read the production Stripe key? Who approved the outbound
email? What changed in the invoice clerk's instructions last Tuesday? How does
a session's work reach `main`? Kortix answers those questions with files you
can read and platform controls that sit below the model, where a prompt cannot
talk its way around them.

A starter scaffold gets one agent running. This repository documents the
controls that come next.

## What you get

- **Per-resource permissions for people and agents.** Account roles, project
  roles, groups and per-agent grants decide who and what may act. An omitted
  grant resolves to none.
- **Secrets encrypted at rest.** The repo stores secret names. Values are
  encrypted in the platform and injected into a session at boot.
- **Connector credentials brokered server-side.** A sandbox carries one scoped
  project token, never a raw third-party key.
- **Allow, ask or block per tool call.** Rules match a tool name, a glob or a
  regular expression, and can test an argument's value.
- **A full audit trail.** Every governed action records the agent, the person
  behind the session, the outcome and a hash of the arguments.
- **One gate to land work.** A session commits to its own branch. An agent can
  only open a change request, and a person merges it.
- **One isolated Linux machine per session.** Each session gets a clean clone
  on a branch named after it, so nothing collides.

## Three commands to a live project

```sh
curl -fsSL https://kortix.com/install | bash   # install the CLI
kortix init                                    # scaffold kortix.yaml, agents/, skills/, memory/
kortix ship                                    # push the repo and bring it live
```

Review what an agent proposes before it lands:

```sh
kortix cr ls
```

## Docs

- [Permissions and roles](docs/permissions-and-roles.md)
- [Secrets and connectors](docs/secrets-and-connectors.md)
- [Audit and change requests](docs/audit-and-change-requests.md)
- [Operations FAQ](docs/faq.md)

## Links

- [Kortix](https://kortix.com): product, pricing and managed cloud.
- [Read the docs](https://kortix.com/docs): the command and config reference.
- [Kortix on GitHub](https://github.com/kortix-ai/suna): the source.
- [Self-hosting walkthrough](https://opensourceclaudecowork.com/self-hosting.html): running the stack on your own hardware.

Kortix is open source (Elastic License 2.0) — self-host, read and modify the code.
