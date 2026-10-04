# Open-Source Permissions and Roles for People and Agents

[Kortix](https://kortix.com) is the open-source AI Management System, and its
permission model governs the people who run agents as well as the agents
themselves. A permission is a named ability. A role is a set of permissions.
An assignment binds one principal to one role on one scope. An agent also
carries a grant list in `kortix.yaml`, and a session may only do what the
person's role and the agent's own grant both allow.

## Two kinds of principal, one grant table

People and agents are governed by the same table. An account has `owner`,
`admin` and `member` roles. Owners and admins hold an implicit Manager role on
every project, so `member` is the account role that takes per-project grants.
A project has `manager` and `member` roles.

An agent's identity is a service account. Assigning a role to an agent uses the
same verbs as assigning one to a person, and an agent that no assignment names
is closed to project members by default.

Groups make the same idea scale. A group is a named set of people you grant a
role to once, then reuse across projects. A role is a named set of permissions.
System roles are read-only references, and custom roles need an enterprise
`rbac` entitlement.

| Layer | Applies to | Where it is set |
| --- | --- | --- |
| Account role | People and groups | Account |
| Project role | People, groups, agents | Project |
| Agent grant | One agent | `kortix.yaml` |
| Tool-call rule | One connector action | Project policy |

## Per-agent reach is deny-by-default

Agents, skills, secrets and connectors are files in one git repo. The
`kortix.yaml` manifest declares what each agent may touch, and an omitted grant
resolves to none:

```yaml
agents:
  invoice-clerk:
    sandbox: python
    connectors: [gmail-read]
    secrets: [STRIPE_API_KEY]
    skills: [reconcile-invoices]
    kortix_permissions: [project.cr.open]
```

Read that block and you know exactly what the invoice clerk can reach: one
connector, one secret, one skill, and permission to open a change request. It
cannot send mail to an address outside its rule, read another project's
credential, or merge its own work.

## Allow, ask or block each call

Every connector action carries one of three answers, and you set them. A rule
matches a tool name, a glob such as `send_*`, or a regular expression. A
condition can point at a value inside the call, so a rule can allow sending to
your own domain and hold everything else. A list argument passes only when
every entry passes, and anything the rule cannot decide resolves toward less
access.

Project-wide rules are evaluated first and cannot be overridden when someone
adds a connector later. The `ask` answer holds the call open while a person
approves it once, approves it for the rest of the session, or denies it. The
agent stays mid-task and resumes where it stopped. The `block` answer is for
actions no approval should lift, such as deleting a customer.

See [Read the docs](https://kortix.com/docs) for the full command and config
reference; the source is [Kortix on GitHub](https://github.com/kortix-ai/suna).
