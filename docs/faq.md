# Open-Source Operations FAQ

[Kortix](https://kortix.com) is the open-source AI Management System. These
are the operations questions a platform team asks after the first demo.

## Should we self-host Kortix or use managed cloud?

Both run the same stack. Kortix runs as one Docker Compose stack with the
frontend, the API, the LLM gateway and a database, and you can run it on a
laptop, a VPS, your VPC or an on-prem network. Agent sessions run on a separate
sandbox provider. Managed cloud removes the operations work. The CLI signs in
per host, so you can switch between cloud and a self-hosted instance without
changing the project.

## Where does the configuration live?

In one git repo you own. `kortix.yaml` holds the machine image, the connectors,
the triggers, the secret names and the per-agent grants. Agents, skills and
memory are markdown and text files in `agents/`, `skills/` and `memory/`, and
the OpenCode runtime config sits in `harnesses/opencode/`. Policy, agents and
config are all text, so every change is a diff.

## How are permissions scoped for people and agents?

Account roles are `owner`, `admin` and `member`; project roles are `manager`
and `member`; groups bind a role once for many people. Each agent also carries
a grant list in `kortix.yaml`, and a session may only do what the person's role
and the agent's grant both allow. Connector calls can be allowed, gated for
approval, or blocked down to one argument.

## How do we review an agent's work?

A session commits to its own branch and opens a change request with a summary
and a unified diff. Run `kortix cr ls` to list what is open, read the diff, and
merge it to land the work. The same review inbox holds connector calls a policy
gated for approval, plus outputs, decisions and batches an agent submits for
sign-off.

## Can an agent merge its own work?

No. A change request is the only way for an agent to land session work on the
default branch, and that merge is deny-by-default for an agent. A person reads
the diff and decides. The same rule covers approvals: an agent may never
resolve its own tool-call approval, so a held call waits for a human.

## Are connector credentials ever in the sandbox?

No. A sandbox carries exactly one Kortix token, scoped to the project. The
gateway resolves the connector credential server-side, attaches it to a single
outbound request and discards it. The raw key is never written into the sandbox
environment, the model never sees it, and the audit ledger stores a hash of the
arguments instead.

See [Read the docs](https://kortix.com/docs) for the full command reference,
and the [self-hosting walkthrough](https://opensourceclaudecowork.com/self-hosting.html)
for running the stack yourself.
